---
title: 'Rövid útmutató: Python-alkalmazás létrehozása Azure Cosmos DB SQL API-fiók használatával'
description: Egy Python-kódmintát mutat be, amellyel csatlakozni lehet az Azure Cosmos DB SQL API-hoz, és lekérdezést lehet végezni vele
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: quickstart
ms.date: 04/06/2021
ms.author: rosouz
ms.custom:
- seodec18
- seo-javascript-september2019
- seo-python-october2019
- devx-track-python
ms.openlocfilehash: de05fdc110e653dd63cf098a8b31ffb81b13b1c5
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365679"
---
# <a name="quickstart-build-a-python-application-using-an-azure-cosmos-db-sql-api-account"></a>Rövid útmutató: Python-alkalmazás létrehozása egy Azure Cosmos DB SQL API-fiók használatával
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Spark v3-összekötő](create-sql-api-spark.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Ebben a rövid útmutatóban egy Azure Cosmos DB SQL API-fiókot fog létrehozni és kezelni a Azure Portal-ból, valamint az Visual Studio Code-ból a GitHubról klónozott Python-alkalmazással. Azure Cosmos DB egy többmodelles adatbázis-szolgáltatás, amely lehetővé teszi dokumentum-, tábla-, kulcs-érték és gráfadatbázisok gyors létrehozására és lekérdezésére globális elosztási és horizontális skáláztatási képességekkel.

## <a name="prerequisites"></a>Előfeltételek

- Egy Cosmos DB fiók. A következő lehetőségek közül választhat:
    * Aktív Azure-előfizetésen belül:
        * [Ingyenes Azure-fiók létrehozása vagy](https://azure.microsoft.com/free) meglévő előfizetés használata 
        * [Visual Studio kreditek](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers)
        * [Azure Cosmos DB ingyenes szint](./optimize-dev-test.md#azure-cosmos-db-free-tier)
    * Aktív Azure-előfizetés nélkül:
        * [Próbálja Azure Cosmos DB ingyenes tesztkörnyezetet,](https://azure.microsoft.com/try/cosmosdb/)amely 30 napig tart.
        * [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator) 
- [Python 2.7 vagy 3.6+](https://www.python.org/downloads/), a `python` végrehajtható `PATH` fájlban.
- [Visual Studio Code](https://code.visualstudio.com/).
- A [Python-bővítmény a Visual Studio Code-hoz.](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview)
- [Git](https://www.git-scm.com/downloads): . 
- [Azure Cosmos DB Pythonhoz készült SQL API SDK használata](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Tároló hozzáadása

Most már használhatja a Adatkezelő eszközét egy Azure Portal tároló létrehozásához. 

1. Válassza **Adatkezelő**  >  **Új tároló létrehozása lehetőséget.** 
    
    A **jobb oldalon** megjelenik a Tároló hozzáadása terület. Előfordulhat, hogy jobbra kell görgetnie, hogy lássa.

    :::image type="content" source="./media/create-sql-api-python/azure-cosmosdb-data-explorer.png" alt-text="Az Azure Portal Adatkezelője a Tároló hozzáadása panellel":::

2. A Tároló **hozzáadása lapon** adja meg az új tároló beállításait.

    |Beállítás|Ajánlott érték|Leírás
    |---|---|---|
    |**Adatbázis-azonosító**|Feladatok|Az új adatbázisnak adja a *Feladatok* nevet. Az adatbázisneveknek 1–255 karakter hosszúságúnak kell lennie, és nem tartalmazhatnak , vagy záró `/, \\, #, ?` szóközt. Jelölje be **az Adatbázis átviteli** sebességének kiépítése beállítást, amely lehetővé teszi az adatbázis számára kibocsátott átviteli sebesség megosztását az adatbázis összes tárolója között. Ez a lehetőség segít a költségmegtakarításban is. |
    |**Átviteli sebesség**|400|Az átviteli sebességet hagyja másodpercenként 400 kérelemegységen (RU/s). Később lehetősége lesz növelni az átviteli sebességet a késés csökkentése érdekében.| 
    |**Tároló azonosítója**|Elemek|Az *új tárolónak* adja az Elemek nevet. A tárolóazonosítók nevére ugyanazok a karakterkorlátozások vonatkoznak, mint az adatbázisnevekre.|
    |**Partíciókulcs**| /kategória| A cikkben leírt minta a */category* partíciókulcsot használja.|
    
    A fenti beállításokon kívül egyedi kulcsokat is hozzáadhat **a** tárolóhoz. Ebben a példában az erre szolgáló mezőt hagyja üresen. Az egyedi kulcsok lehetőséget nyújtanak a fejlesztők számára, hogy adatintegritási réteget adjanak az adatbázishoz. Ha egy tároló létrehozásakor létrehoz egy egyedi kulcs-szabályzatot, azzal biztosítja egy vagy több érték egyediségét partíciókulcsonként. További információt az [Azure Cosmos DB-ben egyedi kulcsaival](unique-keys.md) kapcsolatos cikkben talál.
    
    Kattintson az **OK** gombra. Az Adatkezelő megjeleníti az új adatbázist és tárolót.

## <a name="add-sample-data"></a>Mintaadatok hozzáadása

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Adatok lekérdezése

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most pedig klónozzon egy SQL API-alkalmazást a GitHubról, állítsa be a kapcsolati sztringet, és futtassa az alkalmazást. Ez a rövid útmutató a Python SDK 4-es [verzióját használja.](https://pypi.org/project/azure-cosmos/#history)

1. Nyisson meg egy parancssort, hozzon létre egy git-samples nevű mappát, majd zárja be a parancssort.

    ```cmd
    md "git-samples"
    ```
   Ha Bash-parancssort használ, használja inkább a következő parancsot:

   ```bash
   mkdir "git-samples"
   ```

2. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` parancs használatával váltson az új mappára, ahol telepíteni szeretné a mintaalkalmazást.

    ```bash
    cd "git-samples"
    ```

3. Futtassa a következő parancsot a mintatárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-python-getting-started.git
    ```  

## <a name="update-your-connection-string"></a>A kapcsolati sztring frissítése

Lépjen vissza az Azure Portalra a kapcsolati sztring adataiért, majd másolja be azokat az alkalmazásba.

1. A bal oldali Azure Cosmos DB válassza a kulcsok [Azure Portal](https://portal.azure.com/)a bal oldali navigációs **sávon** a Kulcsokat. A képernyő jobb oldalán található másolási gombokkal másolja  az **URI-t** és az elsődleges kulcsot a *cosmos_get_started.py* fájlba a következő lépésben.

    :::image type="content" source="./media/create-sql-api-dotnet/access-key-and-uri-in-keys-settings-in-the-azure-portal.png" alt-text="Szerezze be a hozzáférési kulcsot és az URI-t a Azure Portal":::

2. A Visual Studio Code-ban nyissa meg a *cosmos_get_started.py* fájlt a *\git-samples\azure-cosmos-db-python-getting-started mappában.*

3. Másolja ki **az URI-értéket** a portálról (a másolás  gombbal), és azt a végpont változójának értékeként a *cosmos_get_started.py fájlban.* 

    `endpoint = 'https://FILLME.documents.azure.com',`

4. Ezután másolja ki az **ELSŐDLEGES KULCS** értékét a  portálról, és azt a kulcs értékeként a *cosmos_get_started.py fájlban.* Ezzel frissítette az alkalmazást az összes olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges. 

    `key = 'FILLME'`

5. Mentse a *cosmos_get_started.py* fájlt.

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező. Ismerje meg a kódban létrehozott adatbázis-erőforrásokat, vagy ugorjon előre [a Kapcsolati sztring frissítése részhez.](#update-your-connection-string)

Az alábbi kódrészletek mind a *cosmos_get_started.py fájlból vannak.*

* A CosmosClient inicializálva van. Ügyeljen arra, hogy frissítse a "végpont" és a "kulcs" értékeket a Kapcsolati sztring [frissítése szakaszban leírtak](#update-your-connection-string) szerint. 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_cosmos_client)]

* A rendszer létrehozza az új adatbázist.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_database_if_not_exists)]

* A rendszer létrehoz egy új tárolót, amely 400 RU/s [átviteli sebességet ad vissza.](request-units.md) A `lastName` partíciókulcsot [választjuk,](partitioning-overview.md#choose-partitionkey)amely lehetővé teszi a tulajdonságra szűrő hatékony lekérdezéseket. 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_container_if_not_exists)]

* Egyes elemek hozzá vannak adva a tárolóhoz. A tárolók elemek (JSON-dokumentumok) gyűjteményei, amelyek különböző sémával is lehetnek. A segítő metódusok egy olyan család ábrázolásait adja vissza, amelyek ```get_[name]_family_item``` JSON-Azure Cosmos DB tárolódnak.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_item)]

* A pont olvasása (kulcsérték-keresések) a metódussal `read_item` történik. Az egyes műveletek [ru-díjának ki](request-units.md) van nyomtatva.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=read_item)]

* A lekérdezések SQL-lekérdezési szintaxissal hajthatóak végre. Mivel a WHERE záradékban a partíciókulcs-értékeit használjuk, a Azure Cosmos DB a lekérdezést hatékonyan a megfelelő partíciókhoz irányítjuk, ami ```lastName``` javítja a teljesítményt.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=query_items)]
   
## <a name="run-the-app"></a>Az alkalmazás futtatása

1. Az Visual Studio Code-ban válassza a View Command Palette **(Parancskatapaletta**  >  **megtekintése) lehetőséget.** 

2. A megjelenő panelen írja be a **Python: Select Interpreter** szöveget, majd válassza ki a használandó Python-verziót.

    A választott értelmező ekkor megjelenik a Visual Studio Code ablakának állapotsorában. 

3. Válassza **az Integrált** terminál megtekintése  >  **lehetőséget** a Visual Studio Code integrált terminál megnyitásához.

4. Az integrált terminálablakban győződjön meg arról, hogy az *azure-cosmos-db-python-getting-started mappában van.* Ha más mappában van, akkor váltson erre a mintamappára az alábbi parancs futtatásával. 

    ```cmd
    cd "\git-samples\azure-cosmos-db-python-getting-started"`
    ```

5. Futtassa az alábbi parancsot az azure-cosmos csomag telepítéséhez. 

    ```python
    pip install --pre azure-cosmos
    ```

    Ha az azure-cosmos csomag telepítésekor „hozzáférés megtagadva” hibaüzenetet kap, akkor [rendszergazdaként kell futtatnia a VS Code alkalmazást](https://stackoverflow.com/questions/37700536/visual-studio-code-terminal-how-to-run-a-command-with-administrator-rights).

6. Futtassa a következő parancsot a minta futtatásához, valamint új dokumentumok létrehozásához és Azure Cosmos DB.

    ```python
    python cosmos_get_started.py
    ```

7. Az új elemek létrejöttének és mentésének megerősítéséhez a Azure Portal válassza Adatkezelő  >  **AzureSampleFamilyDatabase Items (AzureSampleFamilyAdatbáziselemek)**  >  **lehetőséget.** Tekintse meg a létrehozott elemeket. Itt például egy JSON-mintadokumentumot mutatunk be az Andersen-családhoz:
   
   ```json
   {
       "id": "Andersen-1569479288379",
       "lastName": "Andersen",
       "district": "WA5",
       "parents": [
           {
               "familyName": null,
               "firstName": "Thomas"
           },
           {
               "familyName": null,
               "firstName": "Mary Kay"
           }
       ],
       "children": null,
       "address": {
           "state": "WA",
           "county": "King",
           "city": "Seattle"
       },
       "registered": true,
       "_rid": "8K5qAIYtZXeBhB4AAAAAAA==",
       "_self": "dbs/8K5qAA==/colls/8K5qAIYtZXc=/docs/8K5qAIYtZXeBhB4AAAAAAA==/",
       "_etag": "\"a3004d78-0000-0800-0000-5d8c5a780000\"",
       "_attachments": "attachments/",
       "_ts": 1569479288
   }
   ```

## <a name="review-slas-in-the-azure-portal"></a>Tekintse át az SLA-kat az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre Azure Cosmos DB-fiókot, hogyan hozhat létre tárolót a Adatkezelő használatával, és hogyan futtathat Egy Python-alkalmazást a Visual Studio Code-ban. Így már további adatokat importálhat az Azure Cosmos DB-fiókba. 

> [!div class="nextstepaction"]
> [Adatok importálása az Azure Cosmos DB-be az SQL API-hoz](import-data.md)
