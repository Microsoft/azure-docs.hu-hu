---
title: Rövid útmutató – Dokumentum-adatbázis létrehozása a Java használatával a Azure Cosmos DB
description: Ez a rövid útmutató egy Java-kódmintát tartalmaz, amely az SQL API-hoz való csatlakozáshoz és a Azure Cosmos DB lekérdezéséhez használható
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 03/07/2021
ms.author: anfeldma
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: e1f81ddba717dc2a9df02fda82ef74b52da8fd82
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366045"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-sql-api-data"></a>Rövid útmutató: Java-alkalmazás összeállítása az SQL API Azure Cosmos DB adatok kezeléséhez
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

Ebben a rövid útmutatóban egy Azure Cosmos DB SQL API-fiókot fog létrehozni és kezelni a Azure Portal- és egy GitHubról klónozott Java-alkalmazással. Először hozzon létre egy Azure Cosmos DB SQL API-fiókot az Azure Portal használatával, majd hozzon létre egy Java-alkalmazást az SQL Java SDK-val, majd adjon hozzá erőforrásokat Cosmos DB-fiókjához a Java-alkalmazással. Azure Cosmos DB egy többmodelles adatbázis-szolgáltatás, amely lehetővé teszi dokumentum-, tábla-, kulcs-érték és gráfadatbázisok gyors létrehozására és lekérdezésére globális elosztási és horizontális skáláztatási képességekkel.

> [!IMPORTANT]  
> Ez a rövid útmutató csak Azure Cosmos DB Java SDK v4-es verziójához készült. További információkért tekintse meg a Azure Cosmos DB Java SDK [v4](sql-api-sdk-java-v4.md)kibocsátási megjegyzéseit, a Maven-adattárat, a Azure Cosmos DB Java SDK [v4](performance-tips-java-sdk-v4-sql.md)teljesítményével kapcsolatos tippeket és a Azure Cosmos DB Java SDK [v4](troubleshoot-java-sdk-v4-sql.md) hibaelhárítási útmutatóját. [](https://mvnrepository.com/artifact/com.azure/azure-cosmos) Ha jelenleg a v4-esnél régebbi verziót használ, a 4-es verzióra való frissítéshez tekintse meg [Azure Cosmos DB Java SDK v4-re](migrate-java-v4-sdk.md) való áttelepítésről készült útmutatót.
>

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egyet ingyenesen.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Vagy [próbálja Azure Cosmos DB azure-előfizetés nélkül,](https://azure.microsoft.com/try/cosmosdb/) ingyenesen. Használhatja a [Azure Cosmos DB Emulatort](https://aka.ms/cosmosdb-emulator) a és a kulcs `https://localhost:8081` URI-ját `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` is.
- [Java fejlesztői készlet (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). A környezeti változót arra a mappára `JAVA_HOME` mutasson, ahová a JDK telepítve van.
- Egy [Maven bináris archívum.](https://maven.apache.org/download.cgi) Ubuntun futtassa `apt-get install maven` a-t a Maven telepítéséhez.
- [Git](https://www.git-scm.com/downloads): . Ubuntun futtassa `sudo apt-get install git` a-t a Git telepítéséhez.

## <a name="introductory-notes"></a>Bevezető megjegyzések

*A fiók Cosmos DB struktúrája.* Az API-któl vagy a programozási  nyelvtől függetlenül a Cosmos DB-fiókok nulla vagy több adatbázist *tartalmaznak,* egy adatbázis *(DB)* nulla vagy több tárolót *tartalmaz,* a tároló pedig nulla vagy több elemet tartalmaz, ahogyan az alábbi ábrán látható: 

:::image type="content" source="./media/account-databases-containers-items/cosmos-entities.png" alt-text="Azure Cosmos-fiókentitások" border="false":::

Az adatbázisokról, tárolókról és elemekről itt olvashat [bővebben.](account-databases-containers-items.md) Néhány fontos tulajdonság a tároló szintjén van meghatározva, többek között a kiosztott átviteli sebesség és a *partíciókulcs.*  

A kiépített átviteli sebességetkérelemegységekben (RU-kban) mérjük, amelyek pénzbeli árral és a fiók működési költségének jelentős meghatározó tényezői. A kiépített átviteli sebesség tárolónkénti részletességgel vagy adatbázisonkénti részletességgel választható ki, de általában a tárolószintű átviteli sebesség specifikációját részesíti előnyben. Az átviteli sebesség átvitelre való kiépítésről itt olvashat [bővebben.](set-throughput.md)

Amikor elemeket szúr be egy Cosmos DB tárolóba, az adatbázis horizontálisan nő azáltal, hogy további tárterületet és számítási kapacitást ad hozzá a kérések kezeléshez. A tárolási és számítási kapacitás különálló egységekben, úgynevezett partíciókban lesz hozzáadva, és a dokumentumokban egy mezőt kell választania partíciókulcsként, amely az egyes dokumentumokat egy partícióra leképezi.  A partíciók kezelése úgy lehetséges, hogy minden partícióhoz nagyjából egyenlő szelet van rendelve a partíciókulcs-értékek tartományán belül; ezért javasoljuk, hogy válasszon egy viszonylag véletlenszerű vagy egyenletesen elosztott partíciókulcsot. Ellenkező esetben egyes partíciók lényegesen több kérést *látnak*(gyakran 8-65), míg más partíciók lényegesen kevesebb kérést *(hideg* partíciót) látnak, és ezt el kell kerülni. A particionálásról itt olvashat [bővebben.](partitioning-overview.md)

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

A dokumentum-adatbázis létrehozásához először létre kell hoznia egy SQL API-fiókot az Azure Cosmos DB segítségével.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Tároló hozzáadása

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Mintaadatok hozzáadása

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Adatok lekérdezése

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most pedig váltsunk át kódok használatára. Klónozunk egy SQL API-alkalmazást a GitHubról, beállítjuk a kapcsolati sztringet, és futtatjuk az alkalmazást. Látni fogja, mennyire egyszerű programozott módon dolgozni az adatokkal. 

Futtassa a következő parancsot a mintatárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén.

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-getting-started.git
```

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező. Ha meg szeretné ismerni, hogyan jönnek létre az adatbázis erőforrásai a kódban, tekintse át a következő kódrészleteket. Egyéb esetben folytathatja [Az alkalmazás futtatása](#run-the-app) szakasszal. 


# <a name="sync-api"></a>[Sync API](#tab/sync)

### <a name="managing-database-resources-using-the-synchronous-sync-api"></a>Adatbázis-erőforrások kezelése a szinkron (szinkron) API-val

* `CosmosClient` inicializálás. A `CosmosClient` ügyféloldali logikai megjelenítést biztosít az Azure Cosmos adatbázis-szolgáltatás számára. Ezzel az ügyféllel a szolgáltatásra irányuló kérések konfigurálhatók és hajthatók végre.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateSyncClient)]

* `CosmosDatabase` Létrehozása.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* `CosmosContainer` Létrehozása.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* Elem létrehozása a `createItem` metódussal.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateItem)]
   
* A pont olvasása a metódussal `readItem` történik.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=ReadItem)]

* A JSON-kapcsolaton keresztüli SQL-lekérdezések a metódussal vannak `queryItems` hajtva végre.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=QueryItems)]

# <a name="async-api"></a>[Async API](#tab/async)

### <a name="managing-database-resources-using-the-asynchronous-async-api"></a>Adatbázis-erőforrások kezelése az aszinkron (aszinkron) API-val

* Az aszinkron API-hívások azonnal visszatérnek, és nem várnak választ a kiszolgálótól. Ennek fényében az alábbi kódrészletek megfelelő tervezési mintákat mutatnak be a fenti felügyeleti feladatok aszinkron API-val való végrehajtásához.

* `CosmosAsyncClient` inicializálás. A `CosmosAsyncClient` ügyféloldali logikai megjelenítést biztosít az Azure Cosmos adatbázis-szolgáltatás számára. Ezzel az ügyféllel konfigurálhatja és hajthatja végre a szolgáltatásra vonatkozó aszinkron kéréseket.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=CreateAsyncClient)]

* `CosmosAsyncDatabase` Létrehozása.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* `CosmosAsyncContainer` Létrehozása.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* A szinkronizálási API-hoz is az elem létrehozása a `createItem` metódussal valósítható meg. Ez a példa bemutatja, hogyan lehet hatékonyan kiírni számos aszinkron kérést egy reaktív streamre való feliratkozás révén, amely kiírja a kéréseket és megjeleníti az `createItem` értesítéseket. Mivel ez az egyszerű példa a befejezésig fut és leáll, a példányok biztosítják, hogy a program ne legyen leállva az `CountDownLatch` elem létrehozása során. **A megfelelő aszinkron programozási gyakorlat nem blokkolja az aszinkron hívásokat – valós esetben a kérések egy határozatlan ideig futó main() hurokból jönnek létre, így nincs szükség az aszinkron hívásokra való rekeszresztésre.**

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=CreateItem)]
   
* A szinkronizálási API-hoz is a pont olvasása a metódus `readItem` használatával történik.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=ReadItem)]

* A szinkronizálási API-hoz is a JSON-n keresztüli SQL-lekérdezések a metódussal `queryItems` hajthatóak végre.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=QueryItems)]

---

## <a name="run-the-app"></a>Az alkalmazás futtatása

Lépjen vissza az Azure Portalra a kapcsolati sztring adataiért, majd indítsa el az alkalmazást a végpontadatokkal. Ez lehetővé teszi az alkalmazás számára, hogy kommunikáljon az üzemeltetett adatbázissal.

1. A git terminálablakában a `cd` paranccsal lépjen a mintakód mappájába.

    ```bash
    cd azure-cosmos-java-getting-started
    ```

2. A git terminálablakában futtassa a következő parancsot a szükséges Java-csomagok telepítéséhez.

    ```bash
    mvn package
    ```

3. A git terminálablakában a következő paranccsal indítsa el a Java-alkalmazást (cserélje le a SYNCASYNCMODE-t a vagy a kódra, attól függően, hogy melyik mintakódot szeretné futtatni, cserélje le a YOUR_COSMOS_DB_HOSTNAME-t a portálról származó, idézett `sync` URI-értékre, és cserélje le a YOUR_COSMOS_DB_MASTER_KEY-t a portálról származó, idézett elsődleges `async` kulcsra)

    ```bash
    mvn exec:java@SYNCASYNCMODE -DACCOUNT_HOST=YOUR_COSMOS_DB_HOSTNAME -DACCOUNT_KEY=YOUR_COSMOS_DB_MASTER_KEY

    ```

    A terminálablakban értesítést kap a FamilyDB adatbázis létrejöttéről. 
    
4. Az alkalmazás létrehoz egy adatbázist névvel `AzureSampleFamilyDB`
5. Az alkalmazás létrehoz egy tárolót névvel `FamilyContainer`
6. Az alkalmazás objektum- és partíciókulcs-érték (a példánkban lastName) használatával hajt végre pont olvasásokat. 
7. Az alkalmazás lekérdezi az összes vezetéknévvel ('Andersen', 'Wakefield', 'Fog') lévő család lekérdezését.

7. Az alkalmazás nem törli a létrehozott erőforrásokat. A portálra visszaváltva [törölje az erőforrásokat](#clean-up-resources)  a fiókból a felmerülő költségek elkerüléséhez.

## <a name="review-slas-in-the-azure-portal"></a>Tekintse át az SLA-kat az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre Azure Cosmos DB SQL API-fiókot, hogyan hozhat létre dokumentum-adatbázist és -tárolót az Adatkezelő használatával, és hogyan futtathat egy Java-alkalmazást, hogy ugyanezt programozott módon tegye meg. Most már további adatokat importálhat a Azure Cosmos DB fiókjába. 

> [!div class="nextstepaction"]
> [Adatok importálása az Azure Cosmos DB-be](import-data.md)
