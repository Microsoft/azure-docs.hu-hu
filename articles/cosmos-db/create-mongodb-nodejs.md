---
title: 'Rövid útmutató: Node.js MongoDB-alkalmazás csatlakoztatása Azure Cosmos DB'
description: Ez a rövid útmutató bemutatja, hogyan csatlakoztathat egy meglévő, a Node.js írt MongoDB-alkalmazást Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 05/21/2019
ms.custom: seo-javascript-september2019, seo-javascript-october2019, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 588c12addbdbd686038699026bb9dccd8671717c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765232"
---
# <a name="quickstart-migrate-an-existing-mongodb-nodejs-web-app-to-azure-cosmos-db"></a>Rövid útmutató: Meglévő MongoDB-Node.js áttelepítése a Azure Cosmos DB 
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](./mongodb-introduction.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

Ebben a rövid útmutatóban létrehozhat és kezelhet egy Azure Cosmos DB for Mongo DB API-fiókot az Azure Cloud Shell használatával, valamint a GitHubról klónozott MEAN (MongoDB, Express, Angular és Node.js) alkalmazással. Azure Cosmos DB egy többmodelles adatbázis-szolgáltatás, amely lehetővé teszi dokumentum-, tábla-, kulcs-érték és gráfadatbázisok gyors létrehozására és lekérdezésére globális elosztási és horizontális skáláztatási képességekkel.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Vagy [próbálja Azure Cosmos DB azure-előfizetés nélkül,](https://azure.microsoft.com/try/cosmosdb/) ingyenesen. A Azure Cosmos DB [Emulatort is használhatja](https://aka.ms/cosmosdb-emulator) a kapcsolati sztringben. `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true`

- [Node.js](https://nodejs.org/), és a Node.js.

- [Git](https://git-scm.com/downloads): .

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Ehhez a cikkhez az Azure CLI 2.0-s vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.


## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Az alábbi parancsok futtatásával klónozza a mintatárházat. Ez a mintatárház az alapértelmezett [MEAN.js](https://meanjs.org/)-alkalmazást tartalmazza.

1. Nyisson meg egy parancssort, hozzon létre egy git-samples nevű mappát, majd zárja be a parancssort.

    ```bash
    mkdir "C:\git-samples"
    ```

2. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` parancs használatával váltson az új mappára, ahol telepíteni szeretné a mintaalkalmazást.

    ```bash
    cd "C:\git-samples"
    ```

3. Futtassa a következő parancsot a mintatárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén. 

    ```bash
    git clone https://github.com/prashanthmadi/mean
    ```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Ez a Node.js írt MongoDB-alkalmazás Azure Cosmos DB, amely támogatja a MongoDB-ügyfelet. Ez azt jelenti, hogy az alkalmazás számára átlátható, hogy az adatok egy adatbázison Azure Cosmos DB tárolódnak.

Telepítse a szükséges csomagokat, és indítsa el az alkalmazást.

```bash
cd mean
npm install
npm start
```
Az alkalmazás sikertelenül megkísérel csatlakozni egy MongoDB-forráshoz. Lépjen ki az alkalmazásból, amikor a kimenet a következőt adja vissza: „[MongoError: connect ECONNREFUSED 127.0.0.1:27017]”.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Ha telepített Azure CLI-t használ, jelentkezzen be az Azure-előfizetésbe [az az login](/cli/azure/reference-index#az_login) paranccsal, és kövesse a képernyőn megjelenő utasításokat. Az Azure Cloud Shell használata esetén kihagyhatja ezt a lépést.

```azurecli
az login 
``` 
   
## <a name="add-the-azure-cosmos-db-module"></a>Az Azure Cosmos DB modul hozzáadása

Telepített Azure-os parancssori felület használata esetén az `az` parancs futtatásával ellenőrizze, hogy a `cosmosdb` összetevő telepítve van-e. Ha a `cosmosdb` szerepel az alapparancsok listáján, lépjen tovább a következő parancsra. Az Azure Cloud Shell használata esetén kihagyhatja ezt a lépést.

Ha a `cosmosdb` nincs az alapparancsok listáján, telepítse újra az [Azure CLI-t](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre [egy erőforráscsoportot](../azure-resource-manager/management/overview.md) [az az group create gombra.](/cli/azure/group#az_group_create) Az Azure-erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat (például webappokat, adatbázisokat és tárfiókokat). 

A következő példában létrehozunk egy erőforráscsoportot a nyugat-európai régióban. Adjon egyedi nevet az erőforráscsoportnak.

Ha a parancsot Azure Cloud Shell, válassza a Try **It**(Próbálja ki) lehetőséget, kövesse a képernyőn megjelenő utasításokat a bejelentkezéshez, majd másolja a parancsot a parancssorba.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB-fiók létrehozása

Hozzon létre egy Cosmos-fiókot [az az cosmosdb create paranccsal.](/cli/azure/cosmosdb#az_cosmosdb_create)

A következő parancsban helyettesítse be a saját egyedi Cosmos-fióknevét a `<cosmosdb-name>` helyőrző helyére. Ez az egyedi név lesz a Cosmos DB végpont () részeként, ezért egyedinek kell lennie az `https://<cosmosdb-name>.documents.azure.com/` Azure-beli összes Cosmos-fiókban. 

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

A `--kind MongoDB` paraméter lehetővé teszi a MongoDB-ügyfélkapcsolatok használatát.

Az Azure Cosmos DB-fiók létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg. 

> [!NOTE]
> Ez a példa az alapértelmezett JSON formátumot használja az Azure CLI kimeneti formátumaként. Más kimeneti formátum használatához lásd: [Az Azure CLI-parancsok kimeneti formátumai](/cli/azure/format-output-azure-cli).

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb-name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<cosmosdb-name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<cosmosdb-name>",
  "readLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ]
} 
```

## <a name="connect-your-nodejs-application-to-the-database"></a>A Node.js-alkalmazás csatlakoztatása az adatbázishoz

Ebben a lépésben csatlakoztatja MEAN.js mintaalkalmazást az Azure Cosmos DB létrehozott adatbázisfiókhoz. 

<a name="devconfig"></a>
## <a name="configure-the-connection-string-in-your-nodejs-application"></a>A kapcsolati sztring konfigurálása a Node.js-alkalmazásban

A MEAN.js-tárházban nyissa meg a `config/env/local-development.js` fájlt.

Cserélje le a fájl tartalmát a következő kódra. A két helyőrzőt is cserélje le a `<cosmosdb-name>` Cosmos-fiók nevére.

```javascript
'use strict';

module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean-dev?ssl=true&sslverifycertificate=false'
  }
};
```

## <a name="retrieve-the-key"></a>A kulcs lekérése

A Cosmos-adatbázishoz való csatlakozáshoz szüksége van az adatbáziskulcsra. Az [elsődleges kulcs lekérése az az cosmosdb keys list](/cli/azure/cosmosdb/keys#az_cosmosdb_keys_list) paranccsal.

```azurecli-interactive
az cosmosdb keys list --name <cosmosdb-name> --resource-group myResourceGroup --query "primaryMasterKey"
```

Az Azure CLI az alábbi példához hasonló formában jeleníti meg a kimeneti adatokat. 

```json
"RUayjYjixJDWG5xTqIiXjC..."
```

Másolja a `primaryMasterKey` értékét. Illessze be a `<primary_master_key>` fölé a `local-development.js` fájlban.

Mentse a módosításokat.

### <a name="run-the-application-again"></a>Futtassa ismét az alkalmazást.

Futtassa ismét az `npm start` parancsot. 

```bash
npm start
```

Ekkor egy konzolüzenet arról értesíti, hogy a fejlesztőkörnyezet fut. 

Ugrás `http://localhost:3000` a következőre egy böngészőben: . A **felső menüben** válassza a Regisztráció lehetőséget, és próbáljon meg létrehozni két próbafelhasználót. 

A MEAN.js-mintaalkalmazás a felhasználói adatokat az adatbázisban tárolja. Ha a MEAN.js-nek sikerül automatikusan bejelentkeznie a létrehozott felhasználói fiókba, akkor az Azure Cosmos DB-adatbázissal létesített kapcsolat megfelelően működik. 

:::image type="content" source="./media/create-mongodb-nodejs/mongodb-connect-success.png" alt-text="A MEAN.js sikeresen csatlakozik a MongoDB-hez":::

## <a name="view-data-in-data-explorer"></a>Adatok megtekintése az Adatkezelőben

A Cosmos-adatbázisban tárolt adatok megtekinthetők és lekérdezhetőek a Azure Portal.

Az előző lépésben létrehozott felhasználói adatok megtekintéséhez, lekérdezéséhez, valamint az azokkal való munkához böngészőjében jelentkezzen be az [Azure Portalra](https://portal.azure.com).

A felső keresőmezőbe írja be a **következőt: Azure Cosmos DB.** Amikor megnyílik a Cosmos-fiók panelje, válassza ki Cosmos-fiókját. A bal oldali navigációs sávon válassza **a** Adatkezelő lehetőséget. A Gyűjtemények panelen bontsa ki gyűjteményét. Ezt követően megtekintheti a gyűjteményhez tartozó dokumentumokat, lekérdezhet adatokat, valamint létrehozhat és futtathat tárolt eljárásokat, eseményindítókat és felhasználói függvényeket. 

:::image type="content" source="./media/create-mongodb-nodejs/cosmosdb-connect-mongodb-data-explorer.png" alt-text="Adatkezelő az Azure Portalon":::


## <a name="deploy-the-nodejs-application-to-azure"></a>A Node.js-alkalmazás központi telepítése az Azure-ban

Ebben a lépésben üzembe helyezheti a Node.js alkalmazást a Cosmos DB.

Talán észrevette, hogy a korábban módosított konfigurációs fájl a fejlesztési környezetre vonatkozik (`/config/env/local-development.js`). Miután az alkalmazást központilag telepíti az App Service-be, az alapértelmezés szerint az éles környezetben fog futni. Ezért az arra vonatkozó konfigurációs fájlban is el kell végezni a korábbi módosításokat.

A MEAN.js-tárházban nyissa meg a `config/env/production.js` fájlt.

A `db` objektumban cserélje le az `uri` értékét az alábbi példa szerint. A korábbiakhoz hasonlóan, most se felejtse el lecserélni a helyőrzőket.

```javascript
'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean?ssl=true&sslverifycertificate=false',
```

> [!NOTE] 
> A `ssl=true` beállítás a követelmények miatt Cosmos DB fontos. További információ: Kapcsolati [sztring követelményei.](connect-mongodb-account.md#connection-string-requirements)
>
>

A terminálban mentse a módosításait a Gitbe. Mindkét parancsot lemásolhatja és azokat egyszerre is futtathatja.

```bash
git add .
git commit -m "configured MongoDB connection string"
```
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre Azure Cosmos DB MongoDB API-fiókot az Azure Cloud Shell használatával, és hogyan hozhat létre és futtathat egy MEAN.js-alkalmazást, amely felhasználókat ad hozzá a fiókhoz. Így már további adatokat importálhat az Azure Cosmos DB-fiókba.

> [!div class="nextstepaction"]
> [MongoDB adatok importálása az Azure Cosmos DB-be](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)