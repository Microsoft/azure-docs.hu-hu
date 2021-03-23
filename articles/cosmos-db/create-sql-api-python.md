---
title: 'Gyors útmutató: Python-alkalmazás létrehozása Azure Cosmos DB SQL API-fiók használatával'
description: Egy Python-kódmintát mutat be, amellyel csatlakozni lehet az Azure Cosmos DB SQL API-hoz, és lekérdezést lehet végezni vele
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: anfeldma
ms.custom:
- seodec18
- seo-javascript-september2019
- seo-python-october2019
- devx-track-python
ms.openlocfilehash: fee0591622c1ee07b6e954b3cadc208a300ab6a5
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104798782"
---
# <a name="quickstart-build-a-python-application-using-an-azure-cosmos-db-sql-api-account"></a>Gyors útmutató: Python-alkalmazás létrehozása Azure Cosmos DB SQL API-fiók használatával
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Ebben a rövid útmutatóban egy Azure Cosmos DB SQL API-fiókot hoz létre és kezel a Azure Portalból és a Visual Studio Code-ból egy GitHubról klónozott Python-alkalmazással. A Azure Cosmos DB egy többmodelles adatbázis-szolgáltatás, amely lehetővé teszi a dokumentumok, tábla, kulcs-érték és gráf adatbázisok gyors létrehozását és lekérdezését globális terjesztési és horizontális méretezési képességekkel.

## <a name="prerequisites"></a>Előfeltételek

- Egy Cosmos DB-fiók. A lehetőségek a következők:
    * Azure aktív előfizetésen belül:
        * [Hozzon létre egy ingyenes Azure-fiókot](https://azure.microsoft.com/free) , vagy használja a meglévő előfizetését 
        * [Visual Studio havi kreditek](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers)
        * [Ingyenes Azure Cosmos DB szintű](./optimize-dev-test.md#azure-cosmos-db-free-tier)
    * Azure aktív előfizetés nélkül:
        * [Próbálja ki Azure Cosmos db](https://azure.microsoft.com/try/cosmosdb/)ingyen, egy 30 napos tesztelési környezetet.
        * [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator) 
- [Python 2,7 vagy 3.6 +](https://www.python.org/downloads/), a `python` végrehajtható fájllal `PATH` .
- [Visual Studio Code](https://code.visualstudio.com/).
- A [Python-bővítmény a Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview)-hoz.
- [Git](https://www.git-scm.com/downloads). 
- [A Pythonhoz készült SQL API SDK Azure Cosmos DB](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Tároló hozzáadása

Most már használhatja a Azure Portal Adatkezelő eszközét egy adatbázis és egy tároló létrehozásához. 

1. Válassza ki **adatkezelő**  >  **új tárolót**. 
    
    A jobb szélen megjelenik a **tároló hozzáadása** felület, ezért a jobb oldali görgetéshez jobbra kell görgetni a megjelenítéshez.

    :::image type="content" source="./media/create-sql-api-python/azure-cosmosdb-data-explorer.png" alt-text="Az Azure Portal Adatkezelője a Tároló hozzáadása panellel":::

2. A **tároló hozzáadása** lapon adja meg az új tároló beállításait.

    |Beállítás|Ajánlott érték|Leírás
    |---|---|---|
    |**Adatbázis-azonosító**|Feladatok|Az új adatbázisnak adja a *Feladatok* nevet. Az adatbázis nevének 1 és 255 karakter közöttinek kell lennie, és nem tartalmazhat `/, \\, #, ?` szóközt. Tekintse meg az **adatbázis átviteli sebességének** kiosztása lehetőséget, amellyel megoszthatja az adatbázison belül kiosztott átviteli sebességet az adatbázis összes tárolóján. Ez a lehetőség a költségmegtakarítást is segíti. |
    |**Átviteli sebesség**|400|Az átviteli sebesség 400 adategység/másodperc (RU/s) esetén. Később lehetősége lesz növelni az átviteli sebességet a késés csökkentése érdekében.| 
    |**Tároló azonosítója**|Elemek|Adja meg az *elemeket* az új tároló neveként. A tárolóazonosítók nevére ugyanazok a karakterkorlátozások vonatkoznak, mint az adatbázisnevekre.|
    |**Partíciókulcs**| /kategória| A cikkben ismertetett minta a */category* használja a partíciós kulcsként.|
    
    Az előző beállításokon kívül opcionálisan hozzáadhat **egyedi kulcsokat** a tárolóhoz. Ebben a példában az erre szolgáló mezőt hagyja üresen. Az egyedi kulcsok lehetőséget nyújtanak a fejlesztők számára, hogy adatintegritási réteget adjanak az adatbázishoz. A tárolók létrehozásakor egyedi kulcsokra vonatkozó szabályzat létrehozásával biztosíthatja, hogy a partíciós kulcs egy vagy több értéke egyedi legyen. További információt az [Azure Cosmos DB-ben egyedi kulcsaival](unique-keys.md) kapcsolatos cikkben talál.
    
    Kattintson az **OK** gombra. Az Adatkezelő megjeleníti az új adatbázist és tárolót.

## <a name="add-sample-data"></a>Mintaadatok hozzáadása

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Adatok lekérdezése

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most pedig klónozzon egy SQL API-alkalmazást a GitHubról, állítsa be a kapcsolati sztringet, és futtassa az alkalmazást. Ez a rövid útmutató a [PYTHON SDK](https://pypi.org/project/azure-cosmos/#history)4-es verzióját használja.

1. Nyisson meg egy parancssort, hozzon létre egy git-samples nevű mappát, majd zárja be a parancssort.

    ```cmd
    md "git-samples"
    ```
   Ha bash-parancssort használ, Ehelyett használja a következő parancsot:

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

1. A [Azure Portal](https://portal.azure.com/)Azure Cosmos db-fiókjában válassza a bal oldali navigációs menüben a **kulcsok** elemet. A következő lépésben a képernyő jobb oldalán lévő másolási gombokkal másolhatja az **URI** -t és az **elsődleges kulcsot** a *cosmos_get_started.* Copy fájlba.

    :::image type="content" source="./media/create-sql-api-dotnet/access-key-and-uri-in-keys-settings-in-the-azure-portal.png" alt-text="Hozzáférési kulcs és URI beszerzése a Azure Portal kulcsok beállításaiban":::

2. A Visual Studio Code-ban nyissa meg a *cosmos_get_started.* *\git-samples\azure-Cosmos-db-Python-Getting-Started* fájlt.

3. Másolja az **URI** -értéket a portálról (a másolás gombbal), és adja meg a **végpont** változó értékét a *cosmos_get_started.* másolásban. 

    `endpoint = 'https://FILLME.documents.azure.com',`

4. Ezután másolja ki az **elsődleges kulcs** értékét a portálról, és adja meg a **kulcs** értékét *cosmos_get_started.* másolásban. Ezzel frissítette az alkalmazást az összes olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges. 

    `key = 'FILLME'`

5. Mentse a *cosmos_get_started.* a fájlt.

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező. Ismerje meg a kódban létrehozott adatbázis-erőforrásokat, vagy ugorjon előre a [kapcsolódási karakterlánc frissítéséhez](#update-your-connection-string).

Az alábbi kódrészletek mind a *cosmos_get_started.* file fájlból származnak.

* A CosmosClient inicializálva van. Győződjön meg arról, hogy a "végpont" és a "kulcs" értékeket frissíti a [kapcsolódási karakterlánc frissítése](#update-your-connection-string) című szakaszban leírtak szerint. 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_cosmos_client)]

* A rendszer létrehozza az új adatbázist.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_database_if_not_exists)]

* Létrejön egy új tároló, amely a [kiépített átviteli sebesség](request-units.md)400 ru/s. `lastName`A [partíciós kulcsot](partitioning-overview.md#choose-partitionkey)választjuk, amely lehetővé teszi, hogy hatékony lekérdezéseket végezzen a tulajdonság szűrésére. 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_container_if_not_exists)]

* Egyes elemek hozzá vannak adva a tárolóhoz. A tárolók olyan elemek (JSON-dokumentumok) gyűjteményei, amelyek változatos sémával rendelkezhetnek. A segítő metódusok a ```get_[name]_family_item``` Azure Cosmos db JSON-dokumentumként tárolt családjának ábrázolását adják vissza.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_item)]

* A pont olvasása (a kulcs értékének keresése) a `read_item` metódus használatával történik. Minden egyes művelet esetében ki kell nyomtatni az [ru díját](request-units.md) .

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=read_item)]

* A lekérdezés SQL-lekérdezési szintaxis használatával történik. Mivel a WHERE záradékban a partíciós kulcs értékeit használjuk ```lastName``` , Azure Cosmos db hatékonyan irányítja át ezt a lekérdezést a megfelelő partíciókhoz, így javul a teljesítmény.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=query_items)]
   
## <a name="run-the-app"></a>Az alkalmazás futtatása

1. A Visual Studio Code-ban válassza a  >  **parancs paletta** megtekintése lehetőséget. 

2. A megjelenő panelen írja be a **Python: Select Interpreter** szöveget, majd válassza ki a használandó Python-verziót.

    A választott értelmező ekkor megjelenik a Visual Studio Code ablakának állapotsorában. 

3. Válassza   >  az **integrált terminál** megtekintése lehetőséget a Visual Studio Code integrált termináljának megnyitásához.

4. Az integrált terminál ablakban ellenőrizze, hogy az *Azure-Cosmos-db-Python-Getting-Started* mappában van-e. Ha más mappában van, akkor váltson erre a mintamappára az alábbi parancs futtatásával. 

    ```cmd
    cd "\git-samples\azure-cosmos-db-python-getting-started"`
    ```

5. Futtassa az alábbi parancsot az azure-cosmos csomag telepítéséhez. 

    ```python
    pip install --pre azure-cosmos
    ```

    Ha az azure-cosmos csomag telepítésekor „hozzáférés megtagadva” hibaüzenetet kap, akkor [rendszergazdaként kell futtatnia a VS Code alkalmazást](https://stackoverflow.com/questions/37700536/visual-studio-code-terminal-how-to-run-a-command-with-administrator-rights).

6. Futtassa a következő parancsot a minta futtatásához, és hozzon létre és tároljon új dokumentumokat Azure Cosmos DBban.

    ```python
    python cosmos_get_started.py
    ```

7. Az új elemek létrehozásának és mentésének megerősítéséhez a Azure Portal válassza **adatkezelő**  >  **AzureSampleFamilyDatabase**  >  **elemet**. Megtekintheti a létrehozott elemeket. Íme például egy példa az Andersen családhoz tartozó JSON-dokumentumra:
   
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

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre Azure Cosmos DB fiókot, hogyan hozhat létre egy tárolót a Adatkezelő használatával, és hogyan futtathat Python-alkalmazást a Visual Studio Code-ban. Így már további adatokat importálhat az Azure Cosmos DB-fiókba. 

> [!div class="nextstepaction"]
> [Adatok importálása az Azure Cosmos DB-be az SQL API-hoz](import-data.md)
