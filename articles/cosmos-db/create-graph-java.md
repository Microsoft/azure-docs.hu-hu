---
title: Graph-adatbázis létrehozása Javával Azure Cosmos DB
description: Egy Java-kódmintát mutat be, amellyel az Azure Cosmos DB-hez csatlakozhat, és a Gremlin használatával gráfadatokat kérdezhet le.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: java
ms.topic: quickstart
ms.date: 03/26/2019
ms.author: chrande
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 5a793eb69b344e361679759e46db47c78885831e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93360955"
---
# <a name="quickstart-build-a-graph-database-with-the-java-sdk-and-the-azure-cosmos-db-gremlin-api"></a>Gyors útmutató: Graph-adatbázis létrehozása a Java SDK-val és a Azure Cosmos DB Gremlin API-val
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

> [!div class="op_single_selector"]
> * [Gremlin-konzol](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

Ebben a rövid útmutatóban egy Azure Cosmos DB Gremlin-(Graph-) API-fiókot hoz létre és kezel a Azure Portalból, és a GitHubról klónozott Java-alkalmazás használatával adja hozzá az adatokhoz. A Azure Cosmos DB egy többmodelles adatbázis-szolgáltatás, amely lehetővé teszi a dokumentumok, tábla, kulcs-érték és gráf adatbázisok gyors létrehozását és lekérdezését globális terjesztési és horizontális méretezési képességekkel.

## <a name="prerequisites"></a>Előfeltételek
- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- [Java fejlesztői készlet (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Mutasson a `JAVA_HOME` környezeti változóra arra a mappára, ahol a JDK telepítve van.
- A [Maven bináris archívuma](https://maven.apache.org/download.cgi). 
- [Git](https://www.git-scm.com/downloads). 

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

A gráfadatbázis létrehozásához először létre kell hoznia egy Gremlin- (Graph-) adatbázisfiókot az Azure Cosmos DB segítségével.

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Gráf hozzáadása

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most pedig váltsunk át kódok használatára. A következő lépésekben elvégezheti a Gremlin API-alkalmazás klónozását a GitHubról, beállíthatja a kapcsolati sztringet, és futtathatja az alkalmazást. Látni fogja, mennyire egyszerű programozott módon dolgozni az adatokkal.  

1. Nyisson meg egy parancssort, hozzon létre egy git-samples nevű mappát, majd zárja be a parancssort.

    ```bash
    md "C:\git-samples"
    ```

2. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` parancs használatával váltson a mappára, ahol telepíteni szeretné a mintaalkalmazást.  

    ```bash
    cd "C:\git-samples"
    ```

3. Futtassa a következő parancsot a mintatárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-java-getting-started.git
    ```

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező. Ha meg szeretné ismerni, hogyan jönnek létre az adatbázis erőforrásai a kódban, tekintse át a következő kódrészleteket. Egyéb esetben ugorhat [A kapcsolati sztring frissítése](#update-your-connection-information) szakaszra.

A következő kódrészletek mind a *C:\git-samples\azure-Cosmos-db-Graph-Java-Getting-started\src\GetStarted\Program.Java* fájlból származnak.

Ez a Java-konzol alkalmazás egy [GREMLIN API](graph-introduction.md) -adatbázist használ az OSS [Apache TinkerPop](https://tinkerpop.apache.org/) illesztőprogrammal. 

- A Gremlin `Client` inicializálva van a *C:\git-samples\azure-Cosmos-db-Graph-Java-Getting-started\src\remote.YAML* fájlban lévő konfigurációból.

    ```java
    cluster = Cluster.build(new File("src/remote.yaml")).create();
    ...
    client = cluster.connect();
    ```

- A Gremlinnel kapcsolatos lépések sorozatát a program a `client.submit` metódus használatával hajtja végre.

    ```java
    ResultSet results = client.submit(gremlin);

    CompletableFuture<List<Result>> completableFutureResults = results.all();
    List<Result> resultList = completableFutureResults.get();

    for (Result result : resultList) {
        System.out.println(result.toString());
    }
    ```

## <a name="update-your-connection-information"></a>A kapcsolatadatok frissítése

Lépjen vissza az Azure Portalra a kapcsolati adatokért, majd másolja be azokat az alkalmazásba. Ezek a beállítások lehetővé teszik az alkalmazás számára, hogy kommunikáljon az üzemeltetett adatbázissal.

1. A [Azure Portal](https://portal.azure.com/)Azure Cosmos db-fiókjában válassza a **kulcsok** lehetőséget. 

    Másolja az URI érték első részét.

    :::image type="content" source="./media/create-graph-java/copy-access-key-azure-portal.png" alt-text="Hozzáférési kulcs megtekintése és másolása az Azure Portal Kulcsok oldalán":::

2. Nyissa meg az *src/Remote. YAML* fájlt, és illessze be az egyedi azonosító értékét `$name$` a alkalmazásba `hosts: [$name$.graphs.azure.com]` .

    A *távoli. YAML 1.* sora ekkor a következőhöz hasonlóan néz ki: 

    `hosts: [test-graph.graphs.azure.com]`

3. Az `endpoint` értékben módosítsa a `graphs` karakterláncot a következőre: `gremlin.cosmosdb`. (Ha a gráfadatbázis-fiókot 2017. december 20. előtt hozta létre, ne módosítsa a végpont értékét, és folytassa a következő lépéssel.)

    A végpontértéknek most így kell kinéznie:

    `"endpoint": "https://testgraphacct.gremlin.cosmosdb.azure.com:443/"`

4. Az Azure Portalon használja a másolás gombot az ELSŐDLEGES KULCS másolásához, és illessze be a `$masterKey$` helyére a következőben: `password: $masterKey$`.

    A *távoli. YAML 4.* sora ekkor a következőhöz hasonlóan néz ki: 

    `password: 2Ggkr662ifxz2Mg==`

5. A *távoli. YAML 3.* vonalának módosítása innen:

    `username: /dbs/$database$/colls/$collection$`

    felhasználóként a(z) 

    `username: /dbs/sample-database/colls/sample-graph`

    Ha egy egyedi nevet adott a mintaadatbázisának vagy -gráfjának, szükség szerint módosítsa az értékeket.

6. Mentse a *Remote. YAML* fájlt.

## <a name="run-the-console-app"></a>A konzolalkalmazás futtatása

1. A git terminálablakában a `cd` paranccsal lépjen az azure-cosmos-db-graph-java-getting-started mappába.

    ```git
    cd "C:\git-samples\azure-cosmos-db-graph-java-getting-started"
    ```

2. A git terminálablakában futtassa a következő parancsot a szükséges Java-csomagok telepítéséhez.

   ```git
   mvn package
   ```

3. A git terminálablakában futtassa a következő parancsot a Java-alkalmazás elindításához.
    
    ```git
    mvn exec:java -D exec.mainClass=GetStarted.Program
    ```

    A terminálablakban megjelennek a gráfhoz hozzáadandó csúcspontok. 
    
    Ha időtúllépési hibákat észlel, ellenőrizze, hogy megfelelően frissítette-e a kapcsolati adatokat [A kapcsolati adatok frissítése](#update-your-connection-information) szakaszban, és próbálja meg újból futtatni az utolsó parancsot. 
    
    A program leállítása után válassza az ENTER (bevitel) lehetőséget, majd váltson vissza az Internet böngésző Azure Portalére. 

<a id="add-sample-data"></a>
## <a name="review-and-add-sample-data"></a>Áttekintés és mintaadatok hozzáadása

Ezután visszaléphet az Adatkezelőbe, és megtekintheti a gráfhoz hozzáadott csúcspontokat, valamint további adatpontokat is hozzáadhat.

1. A Azure Portal Azure Cosmos DB-fiókjában válassza a **adatkezelő**, majd a **minta-gráf** csomópontot, válassza a **gráf** lehetőséget, majd válassza a **szűrő alkalmazása** lehetőséget. 

   :::image type="content" source="./media/create-graph-java/azure-cosmosdb-data-explorer-expanded.png" alt-text="Képernyőfelvétel: a P I-ből kiválasztott gráf a szűrő alkalmazására szolgáló lehetőséggel.":::

2. A **Találatok** listában megjelennek a gráfhoz hozzáadott új felhasználók. Ha kiválasztja a **ben** elemet, láthatja, hogy a felhasználó kapcsolódik a robin elemhez. A csúcspontokat szabadon mozgathatja húzással, nagyíthat és kicsinyíthet az egérgörgő görgetésével, illetve megnövelheti a gráf méretét a duplaszárú nyíllal. 

   :::image type="content" source="./media/create-graph-java/azure-cosmosdb-graph-explorer-new.png" alt-text="Új csúcspontok az Azure Portal Adatkezelőjében megjelenő gráfban":::

3. Adjunk hozzá néhány új felhasználót. Válassza az **új csúcspont** lehetőséget az adatgráfhoz való adatfelvételhez.

   :::image type="content" source="./media/create-graph-java/azure-cosmosdb-data-explorer-new-vertex.png" alt-text="Képernyőfelvétel: az új csúcspont panel, ahol megadhatja az értékeket.":::

4. A címke mezőbe írja be a *személy* kifejezést.

5. Válassza a **tulajdonság hozzáadása** lehetőséget a következő tulajdonságok hozzáadásához. Egyedi tulajdonságokat hozhat létre a gráfban található minden egyes személy számára. Csak az id kulcsot kötelező megadni.

    kulcs|érték|Jegyzetek
    ----|----|----
    id|ashley|A csúcspont egyedi azonosítója. Ha nem ad meg azonosítót, a rendszer létrehoz egyet.
    gender|female| 
    tech | java | 

    > [!NOTE]
    > Ebben a rövid útmutatóban egy nem particionált gyűjteményt fog létrehozni. Ha azonban a gyűjtemény létrehozásakor megad egy partíciókulcsot és particionált gyűjteményt hoz létre, minden új csúcspontban meg kell adnia kulcsként a partíciókulcsot. 

6. Válassza az **OK** lehetőséget. Előfordulhat, hogy ki kell terjesztenie a képernyőt a képernyő alján lévő **OK** gomb megjelenítéséhez.

7. Válassza újra az **új csúcspontot** , és adjon hozzá egy további új felhasználót. 

8. Adja meg a *person* címkét.

9. Válassza a **tulajdonság hozzáadása** lehetőséget a következő tulajdonságok hozzáadásához:

    kulcs|érték|Jegyzetek
    ----|----|----
    id|rakesh|A csúcspont egyedi azonosítója. Ha nem ad meg azonosítót, a rendszer létrehoz egyet.
    gender|male| 
    school|MIT| 

10. Válassza az **OK** lehetőséget. 

11. Válassza a **szűrő alkalmazása** gombot az alapértelmezett `g.V()` szűrővel a gráf összes értékének megjelenítéséhez. Most már az összes felhasználó megjelenik a **Találatok** listában. 

    Ha további adatokat ad meg, szűrőkkel csökkentheti a találatok számát. Az Adatkezelő alapértelmezés szerint a `g.V()` lekérdezést használja a gráf összes csúcspontjának lekéréséhez. Ezt más [gráflekérdezésre](tutorial-query-graph.md) is módosíthatja, például a `g.V().count()` lekérdezésre, ha azt szeretné, hogy a rendszer JSON formátumban adja vissza a gráf csúcspontjainak számát. Ha módosította a szűrőt, váltson vissza a szűrőre, `g.V()` és válassza a **szűrő alkalmazása** lehetőséget az összes eredmény ismételt megjelenítéséhez.

12. Most már összekapcsolhatja a rakesh és az ashley elemet. Győződjön meg arról, hogy az **Ashley** ki van választva az **eredmények** listájában, majd jelölje be :::image type="content" source="./media/create-graph-java/edit-pencil-button.png" alt-text="a csúcspontok céljainak módosítása egy gráfban":::  a jobb alsó sarokban lévő **célok** mellett. Előfordulhat, hogy szélesebbre kell állítania az ablakot a gomb megjelenítéséhez.

    :::image type="content" source="./media/create-graph-java/azure-cosmosdb-data-explorer-edit-target.png" alt-text="Csúcspont céljának módosítása egy gráfban – Azure CosmosDB":::

13. A **cél** mezőbe írja be a *Böjthe* értéket, és az **Edge felirat** mezőjébe írja be a *Knows* elemet, majd jelölje be a jelölőnégyzetet.

    :::image type="content" source="./media/create-graph-java/azure-cosmosdb-data-explorer-set-target.png" alt-text="Adatkezelő-Azure CosmosDB-beli kapcsolatok hozzáadása":::

14. Ezután válassza ki a **rakesh** elemet a találatok listájából. Láthatja, hogy az ashley és a rakesh elem össze van kapcsolva. 

    :::image type="content" source="./media/create-graph-java/azure-cosmosdb-graph-explorer.png" alt-text="Két csúcspont csatlakozik Adatkezelő-Azure CosmosDB":::

Ezzel befejezte az oktatóanyag erőforrások létrehozásra vonatkozó részét. A gráfhoz továbbra is hozzáadhat csúcspontokat, módosíthatja a meglévő csúcspontokat, és megváltoztathatja a lekérdezéseket. Most pedig tekintsük át az Azure Cosmos DB által biztosított mérőszámokat, majd távolítsuk el az erőforrásokat. 

## <a name="review-slas-in-the-azure-portal"></a>Tekintse át az SLA-kat az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

Ebből a rövid útmutatóból megtudhatta, hogyan hozhat létre egy Azure Cosmos DB fiókot, hogyan hozhat létre egy gráfot a Adatkezelő használatával, és hogyan futtathat olyan Java-alkalmazást, amely a gráfhoz is felveszi az adataikat Az útmutató információira támaszkodva összetett lekérdezéseket hozhat létre és hatékony gráfbejárási logikákat helyezhet üzembe a Gremlin használatával. 

> [!div class="nextstepaction"]
> [Lekérdezés a Gremlin használatával](tutorial-query-graph.md)

