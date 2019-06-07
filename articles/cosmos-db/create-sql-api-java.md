---
title: Egy Azure Cosmos DB dokumentum-adatbázis létrehozása javával "
description: Egy Java-kódmintát mutat be, amellyel csatlakozni lehet az Azure Cosmos DB SQL API-hoz, és lekérdezést lehet végezni vele
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 05/21/2019
ms.author: sngun
ms.openlocfilehash: c97b974919c5aec4c902867604d207cda11e8594
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754294"
---
# <a name="quickstart-build-a-java-application-using-azure-cosmos-db-sql-api-account"></a>Gyors útmutató: Egy Java-alkalmazások Azure Cosmos DB SQL API-fiók használatával


> [!div class="op_single_selector"]
> * [.NET](create-sql-api-dotnet.md)
> * [.NET (előzetes verzió)](create-sql-api-dotnet-preview.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Ez a rövid útmutató azt mutatja be, hogyan hozhatja létre és kezelheti Java-alkalmazással egy Azure Cosmos DB [SQL API](sql-api-introduction.md)-fiók erőforrásait. Először létrehoz egy Azure Cosmos DB SQL API-fiókot az Azure Portallal, majd létrehoz egy Java-alkalmazást az [SQL Java SDK](sql-api-sdk-async-java.md)-val, végül pedig erőforrásokat ad hozzá a Cosmos DB-fiókhoz a Java-alkalmazással. A rövid útmutatóban lévő utasítások bármilyen, Java-programok futtatására alkalmas operációs rendszeren végrehajthatók. Ez a rövid útmutató elvégzése után lesz ismerős létrehozását és módosítását a Cosmos DB-adatbázisok, a felhasználói felületen vagy programozás útján, megismerheti a tárolók.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Továbbá: 

* [Java fejlesztői készlet (JDK) 8-as verzió](https://aka.ms/azure-jdks)
    * Ügyeljen arra, hogy a JAVA_HOME környezeti változó arra a mappára mutasson, ahová a JDK telepítve lett.
* [Maven](https://maven.apache.org/download.cgi) bináris archívum [letöltése](https://maven.apache.org/install.html) és [telepítése](https://maven.apache.org/)
    * Ubuntu rendszeren futtathatja az `apt-get install maven` parancsot a Maven telepítéséhez.
* [Git](https://www.git-scm.com/)
    * Ubuntu rendszeren futtathatja a `sudo apt-get install git` parancsot a Git telepítéséhez.

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

A dokumentum-adatbázis létrehozásához először létre kell hoznia egy SQL API-fiókot az Azure Cosmos DB segítségével.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Tároló felvétele

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Mintaadatok hozzáadása

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Adatok lekérdezése

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most pedig váltsunk át kódok használatára. Klónozunk egy SQL API-alkalmazást a GitHubról, beállítjuk a kapcsolati sztringet, és futtatjuk az alkalmazást. Látni fogja, milyen egyszerű az adatokkal programozott módon dolgozni. 

1. Futtassa a következő parancsot a minta tárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started
    ```

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező. Ha meg szeretné ismerni, hogyan jönnek létre az adatbázis erőforrásai a kódban, tekintse át a következő kódrészleteket. Egyéb esetben folytathatja [Az alkalmazás futtatása](#run-the-app) szakasszal. 

* `AsyncDocumentClient` inicializálás. A [AsyncDocumentClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient) az Azure Cosmos database szolgáltatás ügyféloldali logikai leképezést biztosít. Ezzel az ügyféllel a szolgáltatásra irányuló kérések konfigurálhatók és hajthatók végre.

    ```java
    client = new AsyncDocumentClient.Builder()
             .withServiceEndpoint(YOUR_COSMOS_DB_ENDPOINT)
             .withMasterKeyOrResourceToken(YOUR_COSMOS_DB_MASTER_KEY)
             .withConnectionPolicy(ConnectionPolicy.GetDefault())
             .withConsistencyLevel(ConsistencyLevel.Eventual)
             .build();
    ```

* [Adatbázis](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.database) létrehozása.

    ```java
    Database databaseDefinition = new Database();
    databaseDefinition.setId(databaseName);
    
    client.createDatabase(databaseDefinition, null)
            .toCompletable()
            .await();
    ```

* [DocumentCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.documentcollection) létrehozása.

    ```java
    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.setId(collectionName);

    //...

    client.createCollection(databaseLink, collectionDefinition, requestOptions)
            .toCompletable()
            .await();
    ```

* Dokumentum létrehozása a [createDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.document) metódus használatával.

    ```java
    // Any Java object within your code
    // can be serialized into JSON and written to Azure Cosmos DB
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen");
    // More properties

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    client.createDocument(collectionLink, family, null, true)
            .toCompletable()
            .await();

    ```

* A JSON-on végrehajtott SQL-lekérdezéseket a rendszer a [queryDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.querydocuments?view=azure-java-stable) metódus használatával hajtja végre.

    ```java
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setPageSize(-1);
    queryOptions.setEnableCrossPartitionQuery(true);
    queryOptions.setMaxDegreeOfParallelism(-1);

    String collectionLink = String.format("/dbs/%s/colls/%s",
            databaseName,
            collectionName);
    Iterator<FeedResponse<Document>> it = client.queryDocuments(
            collectionLink,
            "SELECT * FROM Family WHERE Family.lastName = 'Andersen'",
            queryOptions).toBlocking().getIterator();

    System.out.println("Running SQL query...");
    while (it.hasNext()) {
        FeedResponse<Document> page = it.next();
        System.out.println(
                String.format("\tRead a page of results with %d items",
                        page.getResults().size()));
        for (Document doc : page.getResults()) {
            System.out.println(String.format("\t doc %s", doc));
        }
    }
    ```    

## <a name="run-the-app"></a>Az alkalmazás futtatása

Lépjen vissza az Azure Portalra a kapcsolati sztring adataiért, majd indítsa el az alkalmazást a végpontadatokkal. Ez lehetővé teszi az alkalmazás számára, hogy kommunikáljon az üzemeltetett adatbázissal.


1. A git terminálablakában a `cd` paranccsal lépjen a mintakód mappájába.

    ```bash
    cd azure-cosmos-db-sql-api-async-java-getting-started/azure-cosmosdb-get-started
    ```

2. A git terminálablakában futtassa a következő parancsot a szükséges Java-csomagok telepítéséhez.

    ```bash
    mvn package
    ```

3. A git terminálablakában futtassa a következő parancsot a Java-alkalmazás indításához (a YOUR_COSMOS_DB_HOSTNAME helyére a portálról lekért URI-értéket, a YOUR_COSMOS_DB_MASTER_KEY helyére pedig a portálról lekért elsődleges kulcsot írja, idézőjelek között)

    ```bash
    mvn exec:java -DACCOUNT_HOST=YOUR_COSMOS_DB_HOSTNAME -DACCOUNT_KEY=YOUR_COSMOS_DB_MASTER_KEY

    ```

    A terminálablakban értesítést kap a FamilyDB adatbázis létrejöttéről. 
    
4. Nyomjon le egy billentyűt az adatbázis létrehozásához, majd nyomjon le egy másikat a gyűjtemény létrehozásához. 

    Váltson át böngészőjében az Adatkezelőre, ahol láthatja, hogy az már tartalmazza a FamilyDB adatbázist és a FamilyCollection gyűjteményt.

5. Váltson a konzolablakra, és nyomjon le egy billentyűt az első dokumentum létrehozásához, majd egy másikat a második dokumentum létrehozásához. Ezután váltson vissza az Adatkezelőre, hogy megtekintse őket. 

6. Nyomjon le egy billentyűt a lekérdezés indításához. A kimenetet a konzolablakban láthatja. 

7. Az alkalmazás nem törli a létrehozott erőforrásokat. A portálra visszaváltva [törölje az erőforrásokat](#clean-up-resources)  a fiókból a felmerülő költségek elkerüléséhez.

    ![Konzolkimenet](./media/create-sql-api-java/rxjava-console-output.png)


## <a name="review-slas-in-the-azure-portal"></a>Az SLA-k áttekintése az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban bemutattuk hogyan hozzon létre egy Azure Cosmos-fiókot, dokumentum-adatbázis és a tároló az adatkezelő segítségével, és futtathat egy alkalmazást, amely programozottan hajtja végre ugyanezt. Már további adatokat importálhat az Azure Cosmos-tárolóba. 

> [!div class="nextstepaction"]
> [Adatok importálása az Azure Cosmos DB-be](import-data.md)
