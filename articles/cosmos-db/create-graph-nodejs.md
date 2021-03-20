---
title: Azure Cosmos DB Node.js alkalmazás létrehozása a Gremlin API használatával
description: A cikk egy Node.js-kódmintát mutat be, amellyel csatlakozhat egy Azure Cosmos DB-adatbázishoz, és lekérdezéseket hajthat végre
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 06/05/2019
ms.author: chrande
ms.custom: devx-track-js
ms.openlocfilehash: 0b88456bd245f79c38d7829dca76de702f768c06
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93360139"
---
# <a name="quickstart-build-a-nodejs-application-by-using-azure-cosmos-db-gremlin-api-account"></a>Gyors útmutató: Node.js-alkalmazás létrehozása Azure Cosmos DB Gremlin API-fiók használatával
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

> [!div class="op_single_selector"]
> * [Gremlin-konzol](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

Ebben a rövid útmutatóban egy Azure Cosmos DB Gremlin-(Graph-) API-fiókot hoz létre és kezel a Azure Portal, és a GitHubról klónozott Node.js alkalmazás használatával adja hozzá az adatokhoz. A Azure Cosmos DB egy többmodelles adatbázis-szolgáltatás, amely lehetővé teszi a dokumentumok, tábla, kulcs-érték és gráf adatbázisok gyors létrehozását és lekérdezését globális terjesztési és horizontális méretezési képességekkel.

## <a name="prerequisites"></a>Előfeltételek
- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- [Node.js 0.10.29 +](https://nodejs.org/).
- [Git](https://git-scm.com/downloads).

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Gráf hozzáadása

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most pedig klónozunk egy Gremlin API-alkalmazást a GitHubról, beállítjuk a kapcsolati sztringet, majd futtatni fogjuk az alkalmazást. Látni fogja, mennyire egyszerű programozott módon dolgozni az adatokkal. 

1. Nyisson meg egy parancssort, hozzon létre egy git-samples nevű mappát, majd zárja be a parancssort.

    ```bash
    md "C:\git-samples"
    ```

2. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` parancs használatával váltson az új mappára, ahol telepíteni szeretné a mintaalkalmazást.

    ```bash
    cd "C:\git-samples"
    ```

3. Futtassa a következő parancsot a mintatárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started.git
    ```

3. Nyissa meg a megoldásfájlt a Visual Studióban. 

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező. Ha meg szeretné ismerni, hogyan jönnek létre az adatbázis erőforrásai a kódban, tekintse át a következő kódrészleteket. Egyéb esetben ugorhat [A kapcsolati sztring frissítése](#update-your-connection-string) szakaszra. 

Az alábbi kódrészletek mind a *app.js* fájlból származnak.

Ez a konzol alkalmazás a nyílt forráskódú [Gremlin Node.js](https://www.npmjs.com/package/gremlin) illesztőprogramot használja.

* Létrejön a Gremlin-ügyfél.

    ```javascript
    const authenticator = new Gremlin.driver.auth.PlainTextSaslAuthenticator(
        `/dbs/${config.database}/colls/${config.collection}`, 
        config.primaryKey
    )


    const client = new Gremlin.driver.Client(
        config.endpoint, 
        { 
            authenticator,
            traversalsource : "g",
            rejectUnauthorized : true,
            mimeType : "application/vnd.gremlin-v2.0+json"
        }
    );

    ```

  A konfigurációk mind a *config.jsban* szerepelnek, amelyeket a [következő szakaszban](#update-your-connection-string)szerkesztünk.

* A rendszer függvények sorozatát definiálja a különböző Gremlin-műveletek végrehajtásához. Ez az egyik közülük:

    ```javascript
    function addVertex1()
    {
        console.log('Running Add Vertex1'); 
        return client.submit("g.addV(label).property('id', id).property('firstName', firstName).property('age', age).property('userid', userid).property('pk', 'pk')", {
                label:"person",
                id:"thomas",
                firstName:"Thomas",
                age:44, userid: 1
            }).then(function (result) {
                    console.log("Result: %s\n", JSON.stringify(result));
            });
    }
    ```

* Mindegyik függvény végrehajt egy `client.execute` metódust egy Gremlin lekérdezési sztring paraméterrel. Itt egy példa a `g.V().count()` végrehajtására:

    ```javascript
    function countVertices()
    {
        console.log('Running Count');
        return client.submit("g.V().count()", { }).then(function (result) {
            console.log("Result: %s\n", JSON.stringify(result));
        });
    }
    ```

* A fájl végén minden metódust meghívja a rendszer. Így egymás után lesznek végrehajtva:

    ```javascript
    client.open()
    .then(dropGraph)
    .then(addVertex1)
    .then(addVertex2)
    .then(addEdge)
    .then(countVertices)
    .catch((err) => {
        console.error("Error running query...");
        console.error(err)
    }).then((res) => {
        client.close();
        finish();
    }).catch((err) => 
        console.error("Fatal error:", err)
    );
    ```


## <a name="update-your-connection-string"></a>A kapcsolati sztring frissítése

1. Nyissa meg a *config.js* fájlt. 

2. A *config.jsban* adja meg a `config.endpoint` **Gremlin Endpoint** értékkel rendelkező kulcsot a Azure Portal Cosmos db-fiókjának **Áttekintés** lapján. 

    `config.endpoint = "https://<your_Gremlin_account_name>.gremlin.cosmosdb.azure.com:443/";`

    :::image type="content" source="./media/create-graph-nodejs/gremlin-uri.png" alt-text="Hozzáférési kulcs megtekintése és másolása a Azure Portal, áttekintés oldalon":::

3. A *config.jsban* adja meg a config. PrimaryKey értéket az **elsődleges kulcs** értékével az Azure Portal Cosmos db fiókjának **kulcsok** oldaláról. 

    `config.primaryKey = "PRIMARYKEY";`

   :::image type="content" source="./media/create-graph-nodejs/keys.png" alt-text="Azure Portal kulcsok panel":::

4. A config.database és a config.collection értékéhez adja meg az adatbázis és a gráf (tároló) nevét. 

Íme egy példa arra, hogy a befejezett *config.js* fájlnak hogyan kell kinéznie:

```javascript
var config = {}

// Note that this must include the protocol (HTTPS:// for .NET SDK URI or wss:// for Gremlin Endpoint) and the port number
config.endpoint = "https://testgraphacct.gremlin.cosmosdb.azure.com:443/"; 
config.primaryKey = "Pams6e7LEUS7LJ2Qk0fjZf3eGo65JdMWHmyn65i52w8ozPX2oxY3iP0yu05t9v1WymAHNcMwPIqNAEv3XDFsEg==";
config.database = "graphdb"
config.collection = "Persons"

module.exports = config;
```

## <a name="run-the-console-app"></a>A konzolalkalmazás futtatása

1. Nyisson meg egy terminál-ablakot, és módosítsa ( `cd` a parancs használatával) a projektben található fájl *package.js* telepítési könyvtárába.

2. Futtassa az `npm install` parancsot a szükséges npm-modulok (köztük a `gremlin`) telepítéséhez.

3. Futtassa a `node app.js` parancsot egy terminálban a node-alkalmazás elindításához.

## <a name="browse-with-data-explorer"></a>Tallózás az Adatkezelővel

Most visszaléphet az Adatkezelőbe az Azure Portalon, és megtekintheti, lekérdezheti, módosíthatja, és használatba veheti az új gráfadatokat.

Az Adatkezelőben az új adatbázis a **Gráfok** ablaktáblán jelenik meg. Bontsa ki az adatbázist, majd a tárolót, és válassza a **Gráfot**.

A mintaalkalmazás által létrehozott adatok a **Gráf** lap következő panelén jelennek meg a **Szűrő alkalmazása** gomb kiválasztásakor.

A szűrő teszteléséhez hajtsa végre a `g.V()` függvényt a következővel: `.has('firstName', 'Thomas')`. Vegye figyelembe, hogy az érték megkülönbözteti a kis- és nagybetűket.

## <a name="review-slas-in-the-azure-portal"></a>Tekintse át az SLA-kat az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-your-resources"></a>Az erőforrások törlése

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan hozhat létre egy Azure Cosmos DB fiókot, hogyan hozhat létre egy gráfot Adatkezelő használatával, és hogyan futtathat egy Node.js alkalmazást a gráfhoz való adatfelvételhez. Mostantól a Gremlin használatával összetettebb lekérdezéseket is létrehozhat, és hatékony gráfbejárási logikákat implementálhat. 

> [!div class="nextstepaction"]
> [Lekérdezés a Gremlin használatával](tutorial-query-graph.md)
