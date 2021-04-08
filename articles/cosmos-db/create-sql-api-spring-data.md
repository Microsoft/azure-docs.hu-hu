---
title: Rövid útmutató – a Spring-adatAzure Cosmos DB v3 használata dokumentum-adatbázis létrehozásához Azure Cosmos DB használatával
description: Ez a rövid útmutató egy Spring-adatAzure Cosmos DB v3-es kódú mintát tartalmaz, amellyel csatlakozhat a Azure Cosmos DB SQL API-hoz, és lekérdezheti azokat.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 10/06/2020
ms.author: anfeldma
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: f31eb0fa6dbb881f7a09b21b9dd4842fdfd291f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93090290"
---
# <a name="quickstart-build-a-spring-data-azure-cosmos-db-v3-app-to-manage-azure-cosmos-db-sql-api-data"></a>Gyors útmutató: Spring-alapú adatAzure Cosmos DB v3-alkalmazás létrehozása Azure Cosmos DB SQL API-alapú adatkezeléshez
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Ebben a rövid útmutatóban egy Azure Cosmos DB SQL API-fiókot hoz létre és felügyel a Azure Portalból, valamint egy Spring-adatokkal, Azure Cosmos DB v3-es, a GitHubról klónozott alkalmazás használatával. Először létre kell hoznia egy Azure Cosmos DB SQL API-fiókot a Azure Portal használatával, majd létre kell hoznia egy Spring boot-alkalmazást a Spring-adatAzure Cosmos DB v3-összekötő használatával, majd hozzá kell adnia az erőforrásokat a Cosmos DB-fiókjához a Spring boot Application használatával. A Azure Cosmos DB egy többmodelles adatbázis-szolgáltatás, amely lehetővé teszi a dokumentumok, tábla, kulcs-érték és gráf adatbázisok gyors létrehozását és lekérdezését globális terjesztési és horizontális méretezési képességekkel.

> [!IMPORTANT]  
> Ezek a kibocsátási megjegyzések a Spring-adatmennyiségek Azure Cosmos DB 3. verziójára vonatkoznak. [A 2. verzió kibocsátási megjegyzéseit itt](sql-api-sdk-java-spring-v2.md)találja. 
>
> A Spring-alapú adatAzure Cosmos DB csak az SQL API-t támogatja.
>
> Tekintse meg ezeket a cikkeket a más Azure Cosmos DB API-k tavaszi adataival kapcsolatban:
> * [Spring-adatforrások az Apache Cassandra és a Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Spring-adatMongoDB Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring-adatGremlin Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
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
git clone https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started.git
```

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező. Ha meg szeretné ismerni, hogyan jönnek létre az adatbázis erőforrásai a kódban, tekintse át a következő kódrészleteket. Egyéb esetben folytathatja [Az alkalmazás futtatása](#run-the-app) szakasszal. 

### <a name="application-configuration-file"></a>Alkalmazás konfigurációs fájlja

Itt bemutatjuk, hogy a Spring boot és Spring adatkezelési folyamata Hogyan fokozza a felhasználói élményt – a Cosmos-ügyfél létrehozásának és a Cosmos-erőforrásokhoz való csatlakozásának folyamata mostantól a kód helyett a config Az alkalmazás indításakor a Spring boot a következő beállításokat használja az **Application. properties** beállításokban:

```xml
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

Miután létrehozta a Azure Cosmos DB fiókot, adatbázist és tárolót, a konfigurációs fájlban lévő üres elemek kitöltése és a Spring boot/Spring adat automatikusan elvégzi a következőket: (1) hozzon létre egy mögöttes Java SDK `CosmosClient` -példányt az URI-val és a kulccsal, és (2) kapcsolódjon az adatbázishoz és a tárolóhoz. Minden be van állítva – **nincs több erőforrás-kezelési kód!**

### <a name="java-source"></a>Java-forrás

A Spring adatértékek – a Hozzáadás az egyszerű, tiszta, szabványosított és platform-független kezelőfelületről is származik, amely az adattárolók üzemeltetésére használható. A fentiekben összefűzött, a Spring-alapú adatelemzési minta alapján az alábbiakban a szifilisz és a lekérdezési minták találhatók a Azure Cosmos DB dokumentumok a Spring-alapú adatAzure Cosmos DBekkel való manipulálására.

* Elemek létrehozása és frissítése a metódus használatával `save` .

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Create)]
   
* Pont – olvasás a tárházban definiált származtatott lekérdezési módszer használatával. Az `findByIdAndLastName` elvégezés pont – olvasás a következőhöz: `UserRepository` . A metódus nevében említett mezők tavasszal a (z) és a (z) mezőkben megadott pont-olvasás végrehajtásához szükségesek `id` `lastName` .

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Read)]

* Az elemek törlése a használatával `deleteAll` :

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Delete)]

* A származtatott lekérdezés a tárház metódusának neve alapján. A Spring-alapú adatforrások a `UserRepository` `findByFirstName` metódust Java SDK SQL-lekérdezésként implementálják a `firstName` mezőben (ez a lekérdezés nem valósítható meg pont – olvasás):

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Query)]

## <a name="run-the-app"></a>Az alkalmazás futtatása

Lépjen vissza az Azure Portalra a kapcsolati sztring adataiért, majd indítsa el az alkalmazást a végpontadatokkal. Ez lehetővé teszi az alkalmazás számára, hogy kommunikáljon az üzemeltetett adatbázissal.

1. A git terminálablakában a `cd` paranccsal lépjen a mintakód mappájába.

    ```bash
    cd azure-spring-data-cosmos-java-sql-api-getting-started/azure-spring-data-cosmos-java-getting-started/
    ```

2. A git-terminál ablakban a következő parancs használatával telepítse a szükséges Spring-adatAzure Cosmos DB csomagokat.

    ```bash
    mvn clean package
    ```

3. A git-terminál ablakban a következő parancs használatával indítsa el a Spring-adatAzure Cosmos DB alkalmazást:

    ```bash
    mvn spring-boot:run
    ```
    
4. Az alkalmazás betölti az **Application. properties tulajdonságot** , és összekapcsolja az Azure Cosmos db-fiókban lévő erőforrásokat.
5. Az alkalmazás végrehajtja a fent ismertetett, pontokkal kapcsolatos szifilisz-műveleteket.
6. Az alkalmazás egy származtatott lekérdezést fog végezni.
7. Az alkalmazás nem törli az erőforrásokat. Váltson vissza a portálra, és [törölje az erőforrásokat](#clean-up-resources) a fiókból, ha el szeretné kerülni a költségek viselését.

## <a name="review-slas-in-the-azure-portal"></a>Tekintse át az SLA-kat az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy Azure Cosmos DB SQL API-fiókot, hogyan hozhat létre egy dokumentum-adatbázist és-tárolót a Adatkezelő használatával, és hogyan futtathat egy rugós adatalkalmazást, hogy ugyanezt programozott módon végezze el. Mostantól további adatait is importálhatja a Azure Cosmos DB-fiókjába. 

> [!div class="nextstepaction"]
> [Adatok importálása az Azure Cosmos DB-be](import-data.md)