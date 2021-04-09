---
title: 'Gyors útmutató: Cassandra API a Node.js-szel – Azure Cosmos DB'
description: Ez a gyors útmutató azt ismerteti, hogy hogyan használható az Azure Cosmos DB Cassandra API profilalkalmazások létrehozására a Node.js használatával
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 02/10/2021
ms.custom: devx-track-js
ms.openlocfilehash: 126ece1327fa92c9b92c587922f1b8d9335d1a01
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100559282"
---
# <a name="quickstart-build-a-cassandra-app-with-nodejs-sdk-and-azure-cosmos-db"></a>Gyors útmutató: Cassandra-alkalmazás létrehozása Node.js SDK-val és Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

Ebben a rövid útmutatóban létrehoz egy Azure Cosmos DB Cassandra API fiókot, és egy GitHub-Node.js alkalmazást használ a GitHubról egy Cassandra-adatbázis és-tároló létrehozásához. A Azure Cosmos DB egy többmodelles adatbázis-szolgáltatás, amely lehetővé teszi a dokumentumok, tábla, kulcs-érték és gráf adatbázisok gyors létrehozását és lekérdezését globális terjesztési és horizontális méretezési képességekkel.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Alternatív lehetőségként [kipróbálhatja ingyenesen az Azure Cosmos DB-t](https://azure.microsoft.com/try/cosmosdb/) Azure-előfizetés, díjfizetés és elköteleződés nélkül.

Emellett szüksége lesz a következőkre:
* [Node.js](https://nodejs.org/dist/v0.10.29/x64/node-v0.10.29-x64.msi) v 0.10.29 vagy újabb verzió
* [Git](https://git-scm.com/)

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

A dokumentum-adatbázis létrehozásához először létre kell hoznia egy Cassandra-fiókot az Azure Cosmos DB segítségével.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most hozzon létre egy Cassandra API alkalmazást a GitHubról, állítsa be a kapcsolatok karakterláncát, és futtassa. Ilyen egyszerű az adatokkal programozott módon dolgozni. 

1. Nyisson meg egy parancssort. Hozzon létre egy `git-samples` nevű mappát. Ezután zárja be a parancssort.

    ```bash
    md "C:\git-samples"
    ```

2. Nyisson meg egy git-terminálablakot, például a git basht. A `cd` parancs használatával váltson az új mappára, ahol telepíteni szeretné a mintaalkalmazást.

    ```bash
    cd "C:\git-samples"
    ```

3. Futtassa a következő parancsot a mintatárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-nodejs-getting-started.git
    ```

1. Telepítse a Node.js függőségeket a NPM.

    ```bash
    npm install
    ```

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező. Ha meg szeretné ismerni, hogyan hozza létre a kód az adatbázis erőforrásait, tekintse át a következő kódrészleteket. A kódrészletek mind a `uprofile.js` fájlból származnak, amely a `C:\git-samples\azure-cosmos-db-cassandra-nodejs-getting-started` mappában található. Egyéb esetben ugorhat [A kapcsolati sztring frissítése](#update-your-connection-string) szakaszra. 

* A felhasználónév és a jelszó értékei a kapcsolati sztring oldalán állíthatók be az Azure Portalon. 

   ```javascript
    let authProvider = new cassandra.auth.PlainTextAuthProvider(
        config.username,
        config.password
    );
   ```

* A `client` inicializálása contactPoint adataival történik. A contactPoint az Azure Portalról kérhető le.

    ```javascript
    let client = new cassandra.Client({
        contactPoints: [`${config.contactPoint}:10350`],
        authProvider: authProvider,
        localDataCenter: config.localDataCenter,
        sslOptions: {
            secureProtocol: "TLSv1_2_method"
        },
    });
    ```

* A `client` csatlakozik az Azure Cosmos DB Cassandra API-hoz.

    ```javascript
    client.connect();
    ```

* A rendszer létrehoz egy új kulcsteret.

    ```javascript
    var query =
        `CREATE KEYSPACE IF NOT EXISTS ${config.keySpace} WITH replication = {'class': 'NetworkTopologyStrategy', 'datacenter' : '1' }`;
    await client.execute(query);
  }
    ```

* Létrejön egy új tábla.

   ```javascript
    query =
        `CREATE TABLE IF NOT EXISTS ${config.keySpace}.user (user_id int PRIMARY KEY, user_name text, user_bcity text)`;
    await client.execute(query);
   },
   ```

* A rendszer beszúrja a kulcs/érték entitásokat.

    ```javascript
    const arr = [
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (1, 'AdrianaS', 'Seattle')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (2, 'JiriK', 'Toronto')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (3, 'IvanH', 'Mumbai')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (4, 'IvanH', 'Seattle')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (5, 'IvanaV', 'Belgaum')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (6, 'LiliyaB', 'Seattle')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (7, 'JindrichH', 'Buenos Aires')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (8, 'AdrianaS', 'Seattle')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (9, 'JozefM', 'Seattle')`,
    ];
    for (const element of arr) {
        await client.execute(element);
    }
    ```

* Lekérdezés az összes kulcsérték lekéréséhez.

    ```javascript
    query = `SELECT * FROM ${config.keySpace}.user`;
    const resultSelect = await client.execute(query);

    for (const row of resultSelect.rows) {
        console.log(
            "Obtained row: %d | %s | %s ",
            row.user_id,
            row.user_name,
            row.user_bcity
        );
    }
    ```  

* Lekérdezés a kulcs-érték lekéréséhez.

    ```javascript
    query = `SELECT * FROM ${config.keySpace}.user where user_id=1`;
    const resultSelectWhere = await client.execute(query);

    for (const row of resultSelectWhere.rows) {
        console.log(
            "Obtained row: %d | %s | %s ",
            row.user_id,
            row.user_name,
            row.user_bcity
        );
    }
    ```  

* A kapcsolatok lezárása. 

    ```javascript
    client.shutdown();
    ```  

## <a name="update-your-connection-string"></a>A kapcsolati sztring frissítése

Lépjen vissza az Azure Portalra a kapcsolati sztring adataiért, majd másolja be azokat az alkalmazásba. A kapcsolati sztring lehetővé teszi az alkalmazás számára, hogy kommunikáljon az üzemeltetett adatbázissal.

1. A [Azure Portal](https://portal.azure.com/)Azure Cosmos db-fiókjában válassza a **kapcsolatok karakterlánc** lehetőséget. 

1. A :::image type="icon" source="./media/create-cassandra-nodejs/copy.png"::: képernyő jobb oldalán található gomb használatával másolja a legfelső értéket, a kapcsolódási pontot.

    :::image type="content" source="./media/create-cassandra-nodejs/keys.png" alt-text="A CONTACT POINT (Kapcsolódási pont), USERNAME (Felhasználónév) és a PASSWORD (Jelszó) értékek másolása az Azure Portal kapcsolati sztring oldaláról":::

1. Nyissa meg az `config.js` fájlt. 

1. Illessze be a CONTACT POINT értéket a portálról a `'CONTACT-POINT` 9. sorba.

    A 9-es vonalnak ekkor a következőhöz hasonlóan kell kinéznie 

    `contactPoint: "cosmos-db-quickstarts.cassandra.cosmosdb.azure.com",`

1. Másolja a USERNAME értéket a portálról, és illessze be a `<FillMEIN>` helyére a 2. sorban.

    A 2. sornak ekkor a következőképp kell kinéznie: 

    `username: 'cosmos-db-quickstart',`

1. Másolja a PASSWORD értéket a portálról, és illessze be a `USERNAME` helyére a 8. sorban.

    A 8. sornak ekkor a következőképp kell kinéznie:

    `password: '2Ggkr662ifxz2Mg==',`

1. Cserélje le a RÉGIÓt arra az Azure-régióra, amelyben az erőforrást létrehozta.

1. Mentse a `config.js` fájlt.


## <a name="run-the-nodejs-app"></a>A Node.js-alkalmazás futtatása

1. A terminál ablakban ellenőrizze, hogy a korábban klónozott minta könyvtárban van-e:

    ```bash
    cd azure-cosmos-db-cassandra-nodejs-getting-started
    ```

1. Futtassa a Node alkalmazást:

    ```bash
    npm start
    ```

4. Ellenőrizze az eredményt a parancssorban.

    :::image type="content" source="./media/create-cassandra-nodejs/output.png" alt-text="A kimenet megtekintése és ellenőrzése":::

    Nyomja le a CTRL + C billentyűkombinációt a program végrehajtásának leállításához és a konzol ablak bezárásához. 

5. Ha megnyitja az **Adatkezelőt** az Azure Portalon, lekérdezheti és módosíthatja és használhatja az új adatokat. 

    :::image type="content" source="./media/create-cassandra-nodejs/data-explorer.png" alt-text="Adatok megtekintése az Adatkezelőben"::: 

## <a name="review-slas-in-the-azure-portal"></a>Tekintse át az SLA-kat az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

Ebből a rövid útmutatóból megtudhatta, hogyan hozhat létre egy Azure Cosmos DB fiókot a Cassandra API, és hogyan futtathat Cassandra Node.js alkalmazást, amely létrehoz egy Cassandra-adatbázist és-tárolót. Mostantól további adatait is importálhatja a Azure Cosmos DB-fiókjába. 

> [!div class="nextstepaction"]
> [Cassandra-adatok importálása az Azure Cosmos DB-be](cassandra-import-data.md)