---
title: 'Gyors útmutató: TABLE API a node.js-szel – Azure Cosmos DB használatával'
description: Ez a gyors útmutató azt ismerteti, hogy hogyan használható az Azure Cosmos DB Table API profilalkalmazások létrehozására az Azure Portal és a Node.js használatával
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 04/10/2018
ms.author: sngun
ms.openlocfilehash: 57da4fcb6fb1a91477b6c31387ab06205be8138c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60890511"
---
# <a name="quickstart-build-a-table-api-app-with-nodejs-and-azure-cosmos-db"></a>Gyors útmutató: Egy táblát a Node.js és az Azure Cosmos DB API-alkalmazás létrehozása

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](create-table-python.md)
> 

Ez a gyors útmutató azt ismerteti, hogy hogyan használható a Node.js és az Azure Cosmos DB [Table API](table-introduction.md) egy alkalmazás létrehozására egy GitHubról származó példa klónozásával. Ez a gyors útmutató emellett azt is bemutatja, hogyan hozható létre egy Azure Cosmos DB-fiók, és hogyan használható az Adatkezelő táblák és entitások létrehozására a webes alapú Azure Portalon.

Az Azure Cosmos DB a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum-, kulcs/érték, széles oszlopú és gráf típusú adatbázisokat, amelyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket. 

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Továbbá:

* [Node.js](https://nodejs.org/en/)-verzió: 0.10.29-es vagy újabb
* [Git](https://git-scm.com/)

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

> [!IMPORTANT] 
> Létre kell hoznia egy új Table API-fiókot, amely használható az általánosan elérhető Table API SDK-kkal. Az általánosan elérhető SDK-k nem támogatják az előzetes verzióban létrehozott Table API-fiókokat.
>

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Tábla hozzáadása

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Mintaadatok hozzáadása

[!INCLUDE [cosmos-db-create-table-add-sample-data](../../includes/cosmos-db-create-table-add-sample-data.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most pedig klónozunk egy Table-alkalmazást a GitHubról, beállítjuk a kapcsolati sztringet, majd futtatni fogjuk az alkalmazást. Látni fogja, milyen egyszerű az adatokkal programozott módon dolgozni. 

1. Nyisson meg egy parancssort, hozzon létre egy git-samples nevű új mappát, majd zárja be a parancssort.

    ```bash
    md "C:\git-samples"
    ```

2. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` parancs használatával váltson az új mappára, ahol telepíteni szeretné a mintaalkalmazást.

    ```bash
    cd "C:\git-samples"
    ```

3. Futtassa a következő parancsot a minta tárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén.

    ```bash
    git clone https://github.com/Azure-Samples/storage-table-node-getting-started.git
    ```

## <a name="update-your-connection-string"></a>A kapcsolati sztring frissítése

Lépjen vissza az Azure Portalra a kapcsolati sztring adataiért, majd másolja be azokat az alkalmazásba. Ez lehetővé teszi az alkalmazás számára, hogy kommunikáljon az üzemeltetett adatbázissal. 

1. Az [Azure Portalon](https://portal.azure.com/) kattintson a **Kapcsolati sztring** elemre. 

    ![Tekintse meg a kapcsolati sztring szükséges adatait a Kapcsolati sztring ablaktáblán, és másolja őket](./media/create-table-nodejs/connection-string.png)

2. Másolja az ELSŐDLEGES KAPCSOLATI SZTRINGET a jobb oldalon található Másolás gombbal.

3. Nyissa meg az app.config fájlt, és illessze be az értéket a connectionString mezőbe a harmadig sorban. 

    > [!IMPORTANT]
    > Ha a végpont a documents.azure.com címet használja, akkor előzetes fiókkal rendelkezik, és létre kell hoznia egy [új Table API-fiókot](#create-a-database-account), amely használható az általánosan elérhető Table API SDK-kkal.
    >

3. Mentse az app.config fájlt.

Az alkalmazás frissítve lett minden olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges. 

## <a name="run-the-app"></a>Az alkalmazás futtatása

1. A git terminálablakában a `cd` paranccsal lépjen a storage-table-java-getting-started mappába.

    ```
    cd "C:\git-samples\storage-table-node-getting-started"
    ```

2. A következő parancs futtatásával telepítheti helyileg az [azure], [node-uuid], [nconf] és az [async] modulokat, és menthet hozzájuk tartozó bejegyzéseket a package.json fájlban

   ```
   npm install azure-storage node-uuid async nconf --save
   ```

2. A git terminálablakában futtassa a következő parancsokat a Node-alkalmazás elindításához.

    ```
    node ./tableSample.js 
    ```

    A konzolablakban láthatja, hogy a rendszer táblaadatokat ad az Azure Cosmos DB új táblaadatbázisához.

    Lépjen vissza az Adatkezelőbe, ahol lekérdezheti és módosíthatja az új adatokat, valamint dolgozhat azokkal. 

## <a name="review-slas-in-the-azure-portal"></a>Az SLA-k áttekintése az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban bemutattuk, hogyan lehet Azure Cosmos DB-fiókot létrehozni, hogyan lehet az Adatkezelő segítségével táblát készíteni, és hogyan lehet futtatni az alkalmazást.  Most már le tudja kérdezni adatait a Table API segítségével.  

> [!div class="nextstepaction"]
> [Táblaadatok importálása a Table API-ba](table-import.md)
