---
title: Gyors útmutató – dokumentum-adatbázis létrehozása Azure Cosmos DB használatával a Java használatával
description: Ez a rövid útmutató bemutatja a Azure Cosmos DB SQL API-hoz való kapcsolódáshoz és a lekérdezéshez használható Java-mintakód
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: anfeldma
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 4b62b591c408f663fd28d5077af924f785ee66c8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93090409"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-sql-api-data"></a>Gyors útmutató: Java-alkalmazás létrehozása Azure Cosmos DB SQL API-beli adatkezeléshez
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Ebben a rövid útmutatóban egy Azure Cosmos DB SQL API-fiókot hoz létre és felügyel a Azure Portalból, és a GitHubról klónozott Java-alkalmazás használatával. Először hozzon létre egy Azure Cosmos DB SQL API-fiókot a Azure Portal használatával, majd hozzon létre egy Java-alkalmazást az SQL Java SDK használatával, majd adja hozzá az erőforrásokat a Cosmos DB-fiókhoz a Java-alkalmazás használatával. A Azure Cosmos DB egy többmodelles adatbázis-szolgáltatás, amely lehetővé teszi a dokumentumok, tábla, kulcs-érték és gráf adatbázisok gyors létrehozását és lekérdezését globális terjesztési és horizontális méretezési képességekkel.

> [!IMPORTANT]  
> Ez a rövid útmutató csak Azure Cosmos DB Java SDK v4 esetében érhető el. További információkért tekintse meg a Azure Cosmos DB Java SDK v4 [kibocsátási megjegyzéseit](sql-api-sdk-java-v4.md), a [Maven-tárházat](https://mvnrepository.com/artifact/com.azure/azure-cosmos), az Azure Cosmos db Java SDK v4 [teljesítménnyel kapcsolatos TIPPEKET](performance-tips-java-sdk-v4-sql.md), valamint Azure Cosmos db Java SDK v4 [hibaelhárítási útmutatóját](troubleshoot-java-sdk-v4-sql.md) . Ha jelenleg a v4-nél régebbi verziót használ, tekintse meg a következőt: [migrálás Azure Cosmos db Java SDK v4](migrate-java-v4-sdk.md) -re – útmutató a v4-re való frissítéshez.
>

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Vagy [próbálja ki Azure Cosmos db](https://azure.microsoft.com/try/cosmosdb/) ingyen Azure-előfizetés nélkül. Használhatja a [Azure Cosmos db emulátort](https://aka.ms/cosmosdb-emulator) is a `https://localhost:8081` és a kulcs URI-ja használatával `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` .
- [Java fejlesztői készlet (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Mutasson a `JAVA_HOME` környezeti változóra arra a mappára, ahol a JDK telepítve van.
- A [Maven bináris archívuma](https://maven.apache.org/download.cgi). Ubuntu rendszeren futtassa a következő parancsot a `apt-get install maven` Maven telepítéséhez:.
- [Git](https://www.git-scm.com/downloads). Ubuntu rendszeren futtassa a parancsot a `sudo apt-get install git` git telepítéséhez.

## <a name="introductory-notes"></a>Bevezető megjegyzések

*Egy Cosmos DB fiók szerkezete.* Az API-tól vagy programozási nyelvtől függetlenül Cosmos DB *fiók* nulla vagy több *adatbázist* tartalmaz, egy *adatbázis* (db) nulla vagy több *tárolót* tartalmaz, és egy *tároló* nulla vagy több elemet tartalmaz, ahogy az alábbi ábrán is látható:

:::image type="content" source="./media/account-databases-containers-items/cosmos-entities.png" alt-text="Azure Cosmos-fiókok entitásai" border="false":::

Az adatbázisokról, a tárolók és az elemek [itt](account-databases-containers-items.md) találhat további információt. Néhány fontos tulajdonság a tároló szintjén van meghatározva, köztük a *kiosztott átviteli sebesség* és a *partíciós kulcs*. 

A kiépített átviteli sebességet a kérések egységei (*RUs*) mérik, amelyek monetáris díjszabással rendelkeznek, és a fiók működési költsége jelentősen meghatározó tényező. A kiépített átviteli sebesség a tároló részletessége vagy az adatbázis-részletesség alapján választható ki, azonban a tároló szintű átviteli specifikáció általában előnyben részesített. Az átviteli sebességről itt olvashat bővebben [.](set-throughput.md)

Ahogy az elemek bekerülnek egy Cosmos DB tárolóba, az adatbázis horizontálisan nő, ha további tárterületet és számítási műveleteket ad hozzá a kérelmek kezeléséhez. A tárolási és a számítási kapacitás a *partícióknak* nevezett különálló egységekben lett felvéve, és a dokumentumokban egy mezőt kell kiválasztania, amely az egyes dokumentumokat egy partícióra képező partíciós kulcs. A partíciók kezelése az, hogy az egyes partíciók nagyjából egyenlő szeletet kapnak a partíciós kulcsok értékeinek tartományába. Ezért javasoljuk, hogy olyan partíciós kulcsot válasszon, amely viszonylag véletlenszerű vagy egyenletes eloszlású. Ellenkező esetben előfordulhat, hogy egyes partíciók lényegesen több kérést (*gyors partíciót*) látnak, míg más partíciók lényegesen kevesebb kérést látnak (*hideg partíció*), és ezt el kell kerülni. További információ a particionálásról [itt](partitioning-overview.md).

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


# <a name="sync-api"></a>[Szinkronizálási API](#tab/sync)

### <a name="managing-database-resources-using-the-synchronous-sync-api"></a>Adatbázis-erőforrások kezelése a szinkron (Sync) API használatával

* `CosmosClient` inicializálás. Az `CosmosClient` ügyféloldali logikai ábrázolást biztosít az Azure Cosmos Database szolgáltatáshoz. Ezzel az ügyféllel a szolgáltatásra irányuló kérések konfigurálhatók és hajthatók végre.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateSyncClient)]

* `CosmosDatabase` létrehozása.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* `CosmosContainer` létrehozása.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* Az elemek létrehozása a `createItem` metódus használatával.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateItem)]
   
* A pontok olvasása metódus használatával történik `readItem` .

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=ReadItem)]

* A JSON-alapú SQL-lekérdezések végrehajtása a metódus használatával történik `queryItems` .

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=QueryItems)]

# <a name="async-api"></a>[Aszinkron API](#tab/async)

### <a name="managing-database-resources-using-the-asynchronous-async-api"></a>Adatbázis-erőforrások kezelése az aszinkron (aszinkron) API használatával

* Az aszinkron API-hívások azonnal visszatérnek, és nem kell várakozni a kiszolgáló válaszára. Ennek fényében a következő kódrészletek megfelelő tervezési mintákat mutatnak az összes korábbi felügyeleti feladat aszinkron API-val történő végrehajtásához.

* `CosmosAsyncClient` inicializálás. Az `CosmosAsyncClient` ügyféloldali logikai ábrázolást biztosít az Azure Cosmos Database szolgáltatáshoz. Ez az ügyfél a szolgáltatással kapcsolatos aszinkron kérelmek konfigurálásához és végrehajtásához használható.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=CreateAsyncClient)]

* `CosmosAsyncDatabase` létrehozása.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* `CosmosAsyncContainer` létrehozása.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* A szinkronizálási API-hoz hasonlóan az elem létrehozása a metódus használatával történik `createItem` . Ez a példa azt mutatja be, hogyan lehet hatékonyan kiadni számos aszinkron `createItem` kérelmet egy olyan reaktív adatfolyamra való feliratkozással, amely a kérelmeket és a kinyomtatási értesítéseket adja ki Mivel ez az egyszerű példa a befejezésre és a befejezésre fut, a `CountDownLatch` példányok használatával biztosítható, hogy a program nem záruljon le az elemek létrehozásakor. **A megfelelő aszinkron programozási gyakorlat nem blokkolja az aszinkron hívásokat – a reális használati esetekben a fő () ciklusból származó kérelmeket, amelyek határozatlan ideig hajtják végre a zárolást, így nincs szükség az aszinkron hívásokra.**

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=CreateItem)]
   
* A szinkronizálási API-hoz hasonlóan a pont olvasása a metódus használatával történik `readItem` .

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=ReadItem)]

* A szinkronizálási API-hoz hasonlóan a JSON-alapú SQL-lekérdezések is a metódus használatával lesznek elvégezve `queryItems` .

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

3. A git-terminál ablakban a következő paranccsal indítsa el a Java-alkalmazást (a SYNCASYNCMODE helyére `sync` vagy `async` attól függően, hogy melyik mintát kívánja futtatni, cserélje le a YOUR_COSMOS_DB_HOSTNAMEt az idézett URI értékre a portálról, és cserélje le a YOUR_COSMOS_DB_MASTER_KEYt az idézett elsődleges kulcsra a portálról)

    ```bash
    mvn exec:java@SYNCASYNCMODE -DACCOUNT_HOST=YOUR_COSMOS_DB_HOSTNAME -DACCOUNT_KEY=YOUR_COSMOS_DB_MASTER_KEY

    ```

    A terminálablakban értesítést kap a FamilyDB adatbázis létrejöttéről. 
    
4. Az alkalmazás létrehoz egy nevű adatbázist `AzureSampleFamilyDB`
5. Az alkalmazás létrehoz egy nevű tárolót `FamilyContainer`
6. Az alkalmazás elvégzi a pont olvasását az objektumazonosítók és a partíciós kulcs érték használatával (amely a példában szereplő lastName). 
7. Az alkalmazás lekérdezi az elemeket a vezetéknevet tartalmazó összes család lekéréséhez ("Andersen", "Wakefield", "Johnson")

7. Az alkalmazás nem törli a létrehozott erőforrásokat. A portálra visszaváltva [törölje az erőforrásokat](#clean-up-resources)  a fiókból a felmerülő költségek elkerüléséhez.

## <a name="review-slas-in-the-azure-portal"></a>Tekintse át az SLA-kat az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy Azure Cosmos DB SQL API-fiókot, hogyan hozhat létre egy dokumentum-adatbázist és-tárolót a Adatkezelő használatával, és hogyan futtathat egy Java-alkalmazást a programozott módon történő futtatásához. Mostantól további adatait is importálhatja a Azure Cosmos DB-fiókjába. 

> [!div class="nextstepaction"]
> [Adatok importálása az Azure Cosmos DB-be](import-data.md)
