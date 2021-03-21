---
title: 'Gyors útmutató: Node.js MongoDB-alkalmazás összekötése Azure Cosmos DB'
description: Ez a rövid útmutató azt ismerteti, hogyan csatlakoztathatók a Node.jsban írt meglévő MongoDB-alkalmazások Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 05/21/2019
ms.custom: seo-javascript-september2019, seo-javascript-october2019, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 97b26e4b32c7ec1b81eb6227ac29b0574bf088fc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101659970"
---
# <a name="quickstart-migrate-an-existing-mongodb-nodejs-web-app-to-azure-cosmos-db"></a>Gyors útmutató: meglévő MongoDB migrálása Node.js webalkalmazásból Azure Cosmos DB 
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](./mongodb-introduction.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

Ebben a rövid útmutatóban egy Azure Cosmos DB hozhat létre és kezelhet a Mongo DB API-fiókhoz a Azure Cloud Shell használatával, valamint a GitHubról származó MEAN (MongoDB, Express, szögletes és Node.js) alkalmazással. A Azure Cosmos DB egy többmodelles adatbázis-szolgáltatás, amely lehetővé teszi a dokumentumok, tábla, kulcs-érték és gráf adatbázisok gyors létrehozását és lekérdezését globális terjesztési és horizontális méretezési képességekkel.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Vagy [próbálja ki Azure Cosmos db](https://azure.microsoft.com/try/cosmosdb/) ingyen Azure-előfizetés nélkül. Használhatja a [Azure Cosmos db emulátort](https://aka.ms/cosmosdb-emulator) is a kapcsolatok karakterláncával `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true` .

- [Node.js](https://nodejs.org/)és a Node.js működésének ismerete.

- [Git](https://git-scm.com/downloads).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Ehhez a cikkhez az Azure CLI 2,0-es vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.


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

Ez a MongoDB-alkalmazás Node.js kapcsolódik a Azure Cosmos DB-adatbázishoz, amely támogatja a MongoDB-ügyfelet. Ez azt jelenti, hogy transzparens az alkalmazásnak, amelyet az adott Azure Cosmos DB adatbázisban tárolnak.

Telepítse a szükséges csomagokat, és indítsa el az alkalmazást.

```bash
cd mean
npm install
npm start
```
Az alkalmazás sikertelenül megkísérel csatlakozni egy MongoDB-forráshoz. Lépjen ki az alkalmazásból, amikor a kimenet a következőt adja vissza: „[MongoError: connect ECONNREFUSED 127.0.0.1:27017]”.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Ha telepített Azure CLI-t használ, jelentkezzen be az Azure-előfizetésbe az az [login](/cli/azure/reference-index#az-login) paranccsal, és kövesse a képernyőn megjelenő utasításokat. Az Azure Cloud Shell használata esetén kihagyhatja ezt a lépést.

```azurecli
az login 
``` 
   
## <a name="add-the-azure-cosmos-db-module"></a>Az Azure Cosmos DB modul hozzáadása

Telepített Azure-os parancssori felület használata esetén az `az` parancs futtatásával ellenőrizze, hogy a `cosmosdb` összetevő telepítve van-e. Ha a `cosmosdb` szerepel az alapparancsok listáján, lépjen tovább a következő parancsra. Az Azure Cloud Shell használata esetén kihagyhatja ezt a lépést.

Ha a `cosmosdb` nincs az alapparancsok listáján, telepítse újra az [Azure CLI-t](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy [erőforráscsoportot](../azure-resource-manager/management/overview.md) az az [Group Create](/cli/azure/group#az-group-create)paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat (például webappokat, adatbázisokat és tárfiókokat). 

A következő példában létrehozunk egy erőforráscsoportot a nyugat-európai régióban. Adjon egyedi nevet az erőforráscsoportnak.

Ha Azure Cloud Shell használ, válassza a **kipróbálás** lehetőséget, kövesse a képernyőn megjelenő utasításokat a bejelentkezéshez, majd másolja a parancsot a parancssorba.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB-fiók létrehozása

Hozzon létre egy Cosmos-fiókot az az [cosmosdb Create](/cli/azure/cosmosdb#az-cosmosdb-create) paranccsal.

A következő parancsban cserélje ki a saját egyedi Cosmos-fiókjának nevét, ahol megjelenik a `<cosmosdb-name>` helyőrző. Ezt az egyedi nevet fogja használni a Cosmos DB Endpoint () részeként `https://<cosmosdb-name>.documents.azure.com/` , így a névnek egyedinek kell lennie az Azure-beli Cosmos-fiókok között. 

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

Ebben a lépésben a MEAN.js minta alkalmazást az imént létrehozott Azure Cosmos DB adatbázis-fiókhoz kapcsolja. 

<a name="devconfig"></a>
## <a name="configure-the-connection-string-in-your-nodejs-application"></a>A kapcsolati sztring konfigurálása a Node.js-alkalmazásban

A MEAN.js-tárházban nyissa meg a `config/env/local-development.js` fájlt.

Cserélje le a fájl tartalmát a következő kódra. Ügyeljen arra, hogy a két helyőrzőt is cserélje le a `<cosmosdb-name>` Cosmos-fiók nevére.

```javascript
'use strict';

module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean-dev?ssl=true&sslverifycertificate=false'
  }
};
```

## <a name="retrieve-the-key"></a>A kulcs lekérése

A Cosmos-adatbázishoz való kapcsolódáshoz szükség van az adatbázis kulcsára. Az elsődleges kulcs lekéréséhez használja az az [cosmosdb Keys List](/cli/azure/cosmosdb/keys#az-cosmosdb-keys-list) parancsot.

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

Ugrás a `http://localhost:3000` böngészőben. A felső menüben válassza a **regisztráció** lehetőséget, majd próbálja meg két dummy felhasználót létrehozni. 

A MEAN.js-mintaalkalmazás a felhasználói adatokat az adatbázisban tárolja. Ha a MEAN.js-nek sikerül automatikusan bejelentkeznie a létrehozott felhasználói fiókba, akkor az Azure Cosmos DB-adatbázissal létesített kapcsolat megfelelően működik. 

:::image type="content" source="./media/create-mongodb-nodejs/mongodb-connect-success.png" alt-text="A MEAN.js sikeresen csatlakozik a MongoDB-hez":::

## <a name="view-data-in-data-explorer"></a>Adatok megtekintése az Adatkezelőben

A Cosmos-adatbázisban tárolt adatértékek megtekinthetők és lefoglalhatók a Azure Portalban.

Az előző lépésben létrehozott felhasználói adatok megtekintéséhez, lekérdezéséhez, valamint az azokkal való munkához böngészőjében jelentkezzen be az [Azure Portalra](https://portal.azure.com).

A felső keresőmezőbe írja be a **Azure Cosmos db** kifejezést. Amikor megnyílik a Cosmos-fiók panel, válassza ki a Cosmos-fiókját. A bal oldali navigációs sávon válassza a **adatkezelő** lehetőséget. A Gyűjtemények panelen bontsa ki gyűjteményét. Ezt követően megtekintheti a gyűjteményhez tartozó dokumentumokat, lekérdezhet adatokat, valamint létrehozhat és futtathat tárolt eljárásokat, eseményindítókat és felhasználói függvényeket. 

:::image type="content" source="./media/create-mongodb-nodejs/cosmosdb-connect-mongodb-data-explorer.png" alt-text="Adatkezelő az Azure Portalon":::


## <a name="deploy-the-nodejs-application-to-azure"></a>A Node.js-alkalmazás központi telepítése az Azure-ban

Ebben a lépésben üzembe helyezi a Node.js alkalmazást a Cosmos DB.

Talán észrevette, hogy a korábban módosított konfigurációs fájl a fejlesztési környezetre vonatkozik (`/config/env/local-development.js`). Miután az alkalmazást központilag telepíti az App Service-be, az alapértelmezés szerint az éles környezetben fog futni. Ezért az arra vonatkozó konfigurációs fájlban is el kell végezni a korábbi módosításokat.

A MEAN.js-tárházban nyissa meg a `config/env/production.js` fájlt.

A `db` objektumban cserélje le az `uri` értékét az alábbi példa szerint. A korábbiakhoz hasonlóan, most se felejtse el lecserélni a helyőrzőket.

```javascript
'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean?ssl=true&sslverifycertificate=false',
```

> [!NOTE] 
> A `ssl=true` beállítás Cosmos db követelmények miatt fontos. További információ: a [kapcsolatok karakterláncával kapcsolatos követelmények](connect-mongodb-account.md#connection-string-requirements).
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

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy Azure Cosmos DB MongoDB API-fiókot a Azure Cloud Shell használatával, és hogyan hozhat létre és futtathat egy MEAN.js alkalmazást, amellyel felhasználókat adhat a fiókhoz. Így már további adatokat importálhat az Azure Cosmos DB-fiókba.

> [!div class="nextstepaction"]
> [MongoDB adatok importálása az Azure Cosmos DB-be](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)