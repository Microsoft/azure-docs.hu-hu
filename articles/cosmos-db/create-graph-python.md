---
title: 'Gyors útmutató: Gremlin API Python-Azure Cosmos DB'
description: Ez a rövid útmutató azt ismerteti, hogy miként használható az Azure Cosmos DB Gremlin API konzolalkalmazások az Azure Portal és a Python használatával történő létrehozására.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: python
ms.topic: quickstart
ms.date: 03/29/2021
ms.author: chrande
ms.custom: devx-track-python
ms.openlocfilehash: cef397789d5ebcfa95c01e42dac9a80b9e1564e0
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106106942"
---
# <a name="quickstart-create-a-graph-database-in-azure-cosmos-db-using-python-and-the-azure-portal"></a>Gyors útmutató: Graph-adatbázis létrehozása Azure Cosmos DB a Python és a Azure Portal használatával
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

> [!div class="op_single_selector"]
> * [Gremlin-konzol](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

Ebben a rövid útmutatóban egy Azure Cosmos DB Gremlin (Graph) API-fiókot hoz létre és kezel a Azure Portalból, és az adatok hozzáadását a GitHubról klónozott Python-alkalmazás használatával végezheti el. A Azure Cosmos DB egy többmodelles adatbázis-szolgáltatás, amely lehetővé teszi a dokumentumok, tábla, kulcs-érték és gráf adatbázisok gyors létrehozását és lekérdezését globális terjesztési és horizontális méretezési képességekkel.

## <a name="prerequisites"></a>Előfeltételek
- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Vagy [próbálja ki Azure Cosmos db](https://azure.microsoft.com/try/cosmosdb/) ingyen Azure-előfizetés nélkül.
- [Python 3.6 +](https://www.python.org/downloads/) , beleértve a [pip](https://pip.pypa.io/en/stable/installing/) Package telepítőt.
- [Python-illesztőprogram a Gremlin-hez](https://github.com/apache/tinkerpop/tree/master/gremlin-python).
- [Git](https://git-scm.com/downloads).

> [!NOTE]
> Ehhez a rövid útmutatóhoz 2017. december 20. után készült gráfadatbázis-fiókra van szükség. A meglévő fiókok azután támogatják a Pythont, hogy az általános elérhetőség érdekében migrálva lettek.

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

A gráfadatbázis létrehozásához először létre kell hoznia egy Gremlin- (Graph-) adatbázisfiókot az Azure Cosmos DB segítségével.

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Gráf hozzáadása

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most pedig váltsunk át kódok használatára. A következő lépésekben elvégezheti a Gremlin API-alkalmazás klónozását a GitHubról, beállíthatja a kapcsolati sztringet, és futtathatja az alkalmazást. Látni fogja, mennyire egyszerű programozott módon dolgozni az adatokkal.  

1. Nyisson meg egy parancssort, hozzon létre egy git-samples nevű mappát, majd zárja be a parancssort.

    ```bash
    mkdir "./git-samples"
    ```

2. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` parancs használatával váltson a mappára, ahol telepíteni szeretné a mintaalkalmazást.  

    ```bash
    cd "./git-samples"
    ```

3. Futtassa a következő parancsot a mintatárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-python-getting-started.git
    ```

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező. Ha meg szeretné ismerni, hogyan jönnek létre az adatbázis erőforrásai a kódban, tekintse át a következő kódrészleteket. A kódrészletek mind a *C:\git-samples\azure-Cosmos-db-Graph-Python-Getting-Started \\* mappában található *Connect.py* -fájlból származnak. Egyéb esetben ugorhat [A kapcsolati sztring frissítése](#update-your-connection-information) szakaszra.

* A Gremlin `client` inicializálva van az 155-es sorban a *Connect.py*-ben. Ügyeljen rá, hogy a `<YOUR_DATABASE>` és a `<YOUR_CONTAINER_OR_GRAPH>` értékeit a fiókja adatbázisának neve és a gráf neve szerint cserélje le:

    ```python
    ...
    client = client.Client('wss://<YOUR_ENDPOINT>.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_CONTAINER_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ...
    ```

* A *Connect.py* -fájl elején megjelenő Gremlin lépések sorozata. Végrehajtásuk a `client.submitAsync()` metódus használatával történik:

    ```python
    client.submitAsync(_gremlin_cleanup_graph)
    ```

## <a name="update-your-connection-information"></a>A kapcsolatadatok frissítése

Lépjen vissza az Azure Portalra a kapcsolati adatokért, majd másolja be azokat az alkalmazásba. Ezek a beállítások lehetővé teszik az alkalmazás számára, hogy kommunikáljon az üzemeltetett adatbázissal.

1. A [Azure Portal](https://portal.azure.com/)Azure Cosmos db-fiókjában válassza a **kulcsok** lehetőséget. 

    Másolja az URI érték első részét.

    :::image type="content" source="./media/create-graph-python/keys.png" alt-text="Hozzáférési kulcs megtekintése és másolása az Azure Portal Kulcsok oldalán":::

2. Nyissa meg a *Connect.py* fájlt, és az 155. sorban illessze be az URI `<YOUR_ENDPOINT>` -értéket itt:

    ```python
    client = client.Client('wss://<YOUR_ENDPOINT>.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_COLLECTION_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ```

    Az ügyfélobjektum URI részének most a következőhöz hasonlóan kell kinéznie:

    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_COLLECTION_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ```

3. Módosítsa a `client` objektum második paraméterét a `<YOUR_DATABASE>` és `<YOUR_COLLECTION_OR_GRAPH>` sztringek lecseréléséhez. Ha a javasolt értékeket használta, a paraméternek ehhez a kódhoz hasonlóan kell kinéznie:

    `username="/dbs/sample-database/colls/sample-graph"`

    A teljes `client` objektumnak ehhez a kódhoz hasonlóan kell kinéznie:

    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/sample-database/colls/sample-graph", 
        password="<YOUR_PASSWORD>")
    ```

4. A **kulcsok** lapon a másolás gombbal másolja az elsődleges kulcsot, és illessze be `<YOUR_PASSWORD>` a `password=<YOUR_PASSWORD>` paraméterbe.

    A teljes `client` objektumdefiníciónak ehhez a kódhoz hasonlóan kell kinéznie:
    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/sample-database/colls/sample-graph", 
        password="asdb13Fadsf14FASc22Ggkr662ifxz2Mg==")
    ```

6. Mentse a *Connect.py* fájlt.

## <a name="run-the-console-app"></a>A konzolalkalmazás futtatása

1. A git terminálablakban a `cd` paranccsal lépjen az azure-cosmos-db-graph-python-getting-started mappába.

    ```git
    cd "./git-samples\azure-cosmos-db-graph-python-getting-started"
    ```

2. A git terminálablakában futtassa a következő parancsot a szükséges Python-csomagok telepítéséhez.

   ```
   pip install -r requirements.txt
   ```

3. A git terminálablakában futtassa a következő parancsot a Python-alkalmazás elindításához.
    
    ```
    python connect.py
    ```

    A terminálablakban megjelennek a gráfhoz hozzáadandó csúcspontok és élek. 
    
    Ha időtúllépési hibákat észlel, ellenőrizze, hogy megfelelően frissítette-e a kapcsolati adatokat [A kapcsolati adatok frissítése](#update-your-connection-information) szakaszban, és próbálja meg újból futtatni az utolsó parancsot. 
    
    Amikor a program leáll, nyomja le az Enter billentyűt, és lépjen vissza az Azure Portalra a webböngészőben.

<a id="add-sample-data"></a>
## <a name="review-and-add-sample-data"></a>Áttekintés és mintaadatok hozzáadása

A csúcspontok és élek beszúrása után visszatérhet a Adatkezelőra, és megtekintheti a gráfhoz hozzáadott csúcspontokat, és további adatpontokat is hozzáadhat.

1. A Azure Portal Azure Cosmos DB-fiókjában válassza a **adatkezelő**, majd a **minta-gráf** csomópontot, válassza a **gráf** lehetőséget, majd válassza a **szűrő alkalmazása** lehetőséget. 

   :::image type="content" source="./media/create-graph-python/azure-cosmosdb-data-explorer-expanded.png" alt-text="Képernyőfelvétel: a P I-ből kiválasztott gráf a szűrő alkalmazására szolgáló lehetőséggel.":::

2. Figyelje meg, hogy az **eredmények** listájában három új felhasználó kerül be a gráfba. A csúcspontokat szabadon mozgathatja húzással, nagyíthat és kicsinyíthet az egérgörgő görgetésével, illetve megnövelheti a gráf méretét a duplaszárú nyíllal. 

   :::image type="content" source="./media/create-graph-python/azure-cosmosdb-graph-explorer-new.png" alt-text="Új csúcspontok az Azure Portal Adatkezelőjében megjelenő gráfban":::

3. Adjunk hozzá néhány új felhasználót. Az **új csúcspont** gomb kiválasztásával adhat hozzá adatait a gráfhoz.

   :::image type="content" source="./media/create-graph-python/azure-cosmosdb-data-explorer-new-vertex.png" alt-text="Képernyőfelvétel: az új csúcspont panel, ahol megadhatja az értékeket.":::

4. Adja meg a *person* címkét.

5. Válassza a **tulajdonság hozzáadása** lehetőséget a következő tulajdonságok hozzáadásához. Egyedi tulajdonságokat hozhat létre a gráfban található minden egyes személy számára. Csak az id kulcsot kötelező megadni.

    kulcs|érték|Jegyzetek
    ----|----|----
    PK|/pk| 
    id|ashley|A csúcspont egyedi azonosítója. Ha nem ad meg azonosítót, a rendszer létrehoz egyet.
    gender|female| 
    tech | java | 

    > [!NOTE]
    > Ebben a rövid útmutatóban egy nem particionált gyűjteményt hozunk létre. Ha azonban a gyűjtemény létrehozásakor megad egy partíciókulcsot és particionált gyűjteményt hoz létre, minden új csúcspontban meg kell adnia kulcsként a partíciókulcsot. 

6. Válassza az **OK** lehetőséget. Előfordulhat, hogy ki kell terjesztenie a képernyőt a képernyő alján lévő **OK** gomb megjelenítéséhez.

7. Válassza újra az **új csúcspontot** , és adjon hozzá egy további új felhasználót. 

8. Adja meg a *person* címkét.

9. Válassza a **tulajdonság hozzáadása** lehetőséget a következő tulajdonságok hozzáadásához:

    kulcs|érték|Jegyzetek
    ----|----|----
    PK|/pk| 
    id|rakesh|A csúcspont egyedi azonosítója. Ha nem ad meg azonosítót, a rendszer létrehoz egyet.
    gender|male| 
    school|MIT| 

10. Válassza az **OK** lehetőséget. 

11. Válassza a **szűrő alkalmazása** gombot az alapértelmezett `g.V()` szűrővel a gráf összes értékének megjelenítéséhez. Most már az összes felhasználó megjelenik a **Találatok** listában. 

    Ha további adatokat ad meg, szűrőkkel csökkentheti a találatok számát. Az Adatkezelő alapértelmezés szerint a `g.V()` lekérdezést használja a gráf összes csúcspontjának lekéréséhez. Ezt más [gráflekérdezésre](tutorial-query-graph.md) is módosíthatja, például a `g.V().count()` lekérdezésre, ha azt szeretné, hogy a rendszer JSON formátumban adja vissza a gráf csúcspontjainak számát. Ha módosította a szűrőt, váltson vissza a szűrőre, `g.V()` és válassza a **szűrő alkalmazása** lehetőséget az összes eredmény ismételt megjelenítéséhez.

12. Most már összekapcsolhatjuk a rakesh és az ashley elemet. Győződjön meg arról, hogy az **Ashley** ki van választva az **eredmények** listájában, majd kattintson a jobb alsó sarokban a **célok** elem melletti Szerkesztés gombra. Előfordulhat, hogy szélesebbre kell állítania az ablakot a **Tulajdonságok** terület megjelenítéséhez.

    :::image type="content" source="./media/create-graph-python/azure-cosmosdb-data-explorer-edit-target.png" alt-text="Gráfcsúcspont céljának módosítása":::

13. A **cél** *mezőbe írja be* a következőt:, majd a **peremhálózat** mezőjébe írja be a *Knows* elemet, majd jelölje be a jelölőnégyzetet.

    :::image type="content" source="./media/create-graph-python/azure-cosmosdb-data-explorer-set-target.png" alt-text="ashley és rakesh közötti kapcsolat hozzáadása az Adatkezelőben":::

14. Ezután válassza ki a **rakesh** elemet a találatok listájából. Láthatja, hogy az ashley és a rakesh elem össze van kapcsolva. 

    :::image type="content" source="./media/create-graph-python/azure-cosmosdb-graph-explorer.png" alt-text="Két összekapcsolt csúcspont az Adatkezelőben":::

Ezzel befejezte az oktatóanyag erőforrások létrehozásra vonatkozó részét. A gráfhoz továbbra is hozzáadhat csúcspontokat, módosíthatja a meglévő csúcspontokat, és megváltoztathatja a lekérdezéseket. Most pedig tekintsük át az Azure Cosmos DB által biztosított mérőszámokat, majd távolítsuk el az erőforrásokat. 

## <a name="review-slas-in-the-azure-portal"></a>Tekintse át az SLA-kat az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

Ebből a rövid útmutatóból megtudhatta, hogyan hozhat létre egy Azure Cosmos DB fiókot, hogyan hozhat létre egy gráfot a Adatkezelő használatával, és hogyan futtathat egy Python-alkalmazást a gráfhoz való adatfelvételhez. Az útmutató információira támaszkodva összetett lekérdezéseket hozhat létre és hatékony gráfbejárási logikákat helyezhet üzembe a Gremlin használatával. 

> [!div class="nextstepaction"]
> [Lekérdezés a Gremlin használatával](tutorial-query-graph.md)

