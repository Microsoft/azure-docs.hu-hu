---
title: Rövid útmutató – A Spring Data Azure Cosmos DB v3 használata dokumentum-adatbázis létrehozásához az Azure Cosmos DB
description: Ez a rövid útmutató egy Spring Data Azure Cosmos DB v3 kódmintát tartalmaz, amely az SQL API-hoz való csatlakozáshoz és Azure Cosmos DB lekérdezéséhez használható
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 03/07/2021
ms.author: anfeldma
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: a094ebb6b218027c195985312b023ccb8838f703
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365678"
---
# <a name="quickstart-build-a-spring-data-azure-cosmos-db-v3-app-to-manage-azure-cosmos-db-sql-api-data"></a>Rövid útmutató: Spring Data Azure Cosmos DB v3-alkalmazás összeállítása az SQL API Azure Cosmos DB adatok kezeléséhez
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

Ebben a rövid útmutatóban egy Azure Cosmos DB SQL API-fiókot fog létrehozni és kezelni a Azure Portal-ból, valamint egy, a GitHubról klónozott Spring Data Azure Cosmos DB v3 alkalmazással. Először létre kell hoznia egy Azure Cosmos DB SQL API-fiókot az Azure Portal használatával, majd létre kell hoznia egy Spring Boot-alkalmazást a Spring Data Azure Cosmos DB v3-összekötővel, majd erőforrásokat kell hozzáadnia Cosmos DB-fiókjához a Spring Boot-alkalmazással. Azure Cosmos DB egy többmodelles adatbázis-szolgáltatás, amely lehetővé teszi dokumentum-, tábla-, kulcs-érték és gráfadatbázisok gyors létrehozására és lekérdezésére globális elosztási és horizontális skáláztatási képességekkel.

> [!IMPORTANT]  
> Ezek a kibocsátási megjegyzések a Spring Data Azure Cosmos DB. A 2. verzió kibocsátási [megjegyzéseit itt találja.](sql-api-sdk-java-spring-v2.md) 
>
> A Spring Data Azure Cosmos DB csak az SQL API-t támogatja.
>
> Az alábbi cikkekben további információt talál a Spring Data-ről más Azure Cosmos DB API-król:
> * [Spring Data for Apache Cassandra with Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Spring Data MongoDB with Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring Data Gremlin with Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egyet ingyenesen.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Vagy [próbálja Azure Cosmos DB azure-előfizetés nélkül,](https://azure.microsoft.com/try/cosmosdb/) ingyenesen. Használhatja a [Azure Cosmos DB Emulatort](https://aka.ms/cosmosdb-emulator) a és a kulcs `https://localhost:8081` URI-ját `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` is.
- [Java fejlesztői készlet (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). A környezeti változót arra a mappára `JAVA_HOME` mutasson, ahová a JDK telepítve van.
- Egy [Bináris Maven-archívum.](https://maven.apache.org/download.cgi) Ubuntun futtassa az `apt-get install maven` -t a Maven telepítéséhez.
- [Git](https://www.git-scm.com/downloads): . Ubuntun futtassa `sudo apt-get install git` a-t a Git telepítéséhez.

## <a name="introductory-notes"></a>Bevezető megjegyzések

*A fiók Cosmos DB struktúrája.* Az API-któl vagy a programozási  nyelvtől függetlenül a Cosmos DB-fiókok nulla vagy több adatbázist *tartalmaznak,* egy adatbázis *(DB)* nulla vagy több tárolót *tartalmaz,* *a* tároló pedig nulla vagy több elemet tartalmaz, ahogyan az alábbi ábrán látható:

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
git clone https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started.git
```

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező. Ha meg szeretné ismerni, hogyan jönnek létre az adatbázis erőforrásai a kódban, tekintse át a következő kódrészleteket. Egyéb esetben folytathatja [Az alkalmazás futtatása](#run-the-app) szakasszal. 

### <a name="application-configuration-file"></a>Alkalmazáskonfigurációs fájl

Itt bemutatjuk, Spring Boot és a Spring Data hogyan javítja a felhasználói élményt – a Cosmos-ügyfél létrehozásának és a Cosmos-erőforrásokhoz való csatlakozásnak folyamata mostantól nem kód, hanem konfiguráció. Az alkalmazás Spring Boot a sablont az application.properties fájlban megadott **beállításokkal kezeli:**

```xml
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

Miután létrehozott egy Azure Cosmos DB-fiókot, -adatbázist és -tárolót, csak töltse ki az üres értékeket a konfigurációs fájlban, és az Spring Boot/Spring Data automatikusan a következőket fogja tenni: (1) hozzon létre egy mögöttes Java SDK-példányt az URI-val és a kulccsal, és (2) csatlakozzon az adatbázishoz és a `CosmosClient` tárolóhoz. Minden be van állítva – **nincs több erőforrás-kezelési kód!**

### <a name="java-source"></a>Java-forrás

A Spring Data értékadója az egyszerű, letisztult, szabványosított és platformfüggetlen adattár-üzemeltetési felületből is származik. A fent hivatkozott Spring Data GitHub-mintára építve az alábbiakban CRUD- és lekérdezésmintákat olvashat a Azure Cosmos DB-dokumentumok Spring Data Azure Cosmos DB.

* Elem létrehozása és frissítése a `save` metódussal.

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Create)]
   
* Pontleolvass az adattárban definiált származtatott lekérdezési metódussal. A `findByIdAndLastName` végrehajtja a pont-olvasást a `UserRepository` számára. A metódus nevében említett mezők miatt a Spring Data végrehajt egy, a és a mező által definiált `id` `lastName` pont-olvasást:

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Read)]

* Elem törlése a `deleteAll` használatával:

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Delete)]

* Az adattár metódusának neve alapján származtatott lekérdezés. A Spring Data Java SDK SQL-lekérdezésként implementálja a metódust a mezőben (ezt a lekérdezést nem lehetett pont olvasásra `UserRepository` `findByFirstName` `firstName` használni):

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Query)]

## <a name="run-the-app"></a>Az alkalmazás futtatása

Lépjen vissza az Azure Portalra a kapcsolati sztring adataiért, majd indítsa el az alkalmazást a végpontadatokkal. Ez lehetővé teszi az alkalmazás számára, hogy kommunikáljon az üzemeltetett adatbázissal.

1. A git terminálablakában a `cd` paranccsal lépjen a mintakód mappájába.

    ```bash
    cd azure-spring-data-cosmos-java-sql-api-getting-started/azure-spring-data-cosmos-java-getting-started/
    ```

2. A git terminálablakában használja a következő parancsot a szükséges Spring Data-Azure Cosmos DB telepítéséhez.

    ```bash
    mvn clean package
    ```

3. A git terminálablakában a következő paranccsal indítsa el a Spring Data Azure Cosmos DB alkalmazást:

    ```bash
    mvn spring-boot:run
    ```
    
4. Az alkalmazás **betölti az application.properties tulajdonságot,** és összekapcsolja a Azure Cosmos DB erőforrásait.
5. Az alkalmazás elvégzi a fent leírt CRUD-műveleteket.
6. Az alkalmazás származtatott lekérdezést hajt végre.
7. Az alkalmazás nem törli az erőforrásokat. Ha el szeretné [](#clean-up-resources) kerülni a költségeket, váltson vissza a portálra az erőforrások fiókból való megtisztítása érdekében.

## <a name="review-slas-in-the-azure-portal"></a>Tekintse át az SLA-kat az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre Azure Cosmos DB SQL API-fiókot, hogyan hozhat létre dokumentum-adatbázist és -tárolót az Adatkezelő használatával, és hogyan futtathat egy Spring Data-alkalmazást, hogy ugyanezt programozott módon tegye meg. Most már további adatokat importálhat a Azure Cosmos DB fiókjába. 

> [!div class="nextstepaction"]
> [Adatok importálása az Azure Cosmos DB-be](import-data.md)