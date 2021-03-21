---
title: Java-alkalmazás Azure Cosmos DB Cassandra API a Java 3,0 SDK-val
description: Ez a rövid útmutató azt ismerteti, hogyan használható a Azure Cosmos DB Cassandra API a Azure Portal és a Java 3,0 SDK-val való profil-alkalmazás létrehozásához.
ms.service: cosmos-db
author: TheovanKraay
ms.author: thvankra
ms.subservice: cosmosdb-cassandra
ms.devlang: java
ms.topic: quickstart
ms.date: 05/18/2020
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: df7f857edc4c1296b6a6e1fae13d70aa4f178728
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93099772"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-cassandra-api-data-v3-driver"></a>Gyors útmutató: Java-alkalmazás létrehozása Azure Cosmos DB Cassandra API-alapú adatkezeléshez (v3-illesztőprogram)
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

Ebben a rövid útmutatóban egy Azure Cosmos DB Cassandra API-fiókot hoz létre, és egy GitHub-ből származó Cassandra Java-alkalmazást hoz létre, amely egy Cassandra-adatbázist és egy tárolót használ a Java-hoz készült [v3. x Apache Cassandra-illesztőprogramok](https://github.com/datastax/java-driver/tree/3.x) használatával. A Azure Cosmos DB egy többmodelles adatbázis-szolgáltatás, amely lehetővé teszi a dokumentumok, tábla, kulcs-érték és gráf adatbázisok gyors létrehozását és lekérdezését globális terjesztési és horizontális méretezési képességekkel.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Vagy [próbálja ki Azure Cosmos db](https://azure.microsoft.com/try/cosmosdb/) ingyen Azure-előfizetés nélkül.
- [Java fejlesztői készlet (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Mutasson a `JAVA_HOME` környezeti változóra arra a mappára, ahol a JDK telepítve van.
- A [Maven bináris archívuma](https://maven.apache.org/download.cgi). Ubuntu rendszeren futtassa a következő parancsot a `apt-get install maven` Maven telepítéséhez:.
- [Git](https://www.git-scm.com/downloads). Ubuntu rendszeren futtassa a parancsot a `sudo apt-get install git` git telepítéséhez.

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

A dokumentum-adatbázis létrehozásához először létre kell hoznia egy Cassandra-fiókot az Azure Cosmos DB segítségével.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most pedig váltsunk át kódok használatára. A következő lépésekben elvégezheti a Cassandra-alkalmazás klónozását a GitHubról, beállíthatja a kapcsolati sztringet, és futtathatja az alkalmazást. Látni fogja, mennyire egyszerű programozott módon dolgozni az adatokkal. 

1. Nyisson meg egy parancssort. Hozzon létre egy `git-samples` nevű mappát. Ezután zárja be a parancssort.

    ```bash
    md "C:\git-samples"
    ```

2. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` parancs használatával váltson az új mappára, ahol telepíteni szeretné a mintaalkalmazást.

    ```bash
    cd "C:\git-samples"
    ```

3. Futtassa a következő parancsot a mintatárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started.git
    ```

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező. Ha meg szeretné ismerni, hogyan hozza létre a kód az adatbázis erőforrásait, tekintse át a következő kódrészleteket. Egyéb esetben ugorhat [A kapcsolati sztring frissítése](#update-your-connection-string) szakaszra. Ezek a kódrészletek mind a *src/Main/Java/com/Azure/cosmosdb/Cassandra/util/CassandraUtils. Java* fájlból származnak.  

* Be van állítva a Cassandra Host, a port, a Felhasználónév, a jelszó és a TLS/SSL-beállítások. A kapcsolati sztring adatai az Azure Portal kapcsolati sztring oldaláról származnak.

   ```java
   cluster = Cluster.builder().addContactPoint(cassandraHost).withPort(cassandraPort).withCredentials(cassandraUsername, cassandraPassword).withSSL(sslOptions).build();
   ```

* A `cluster` csatlakozik az Azure Cosmos DB Cassandra API-hoz, és hozzáférésre visszaad egy munkamenetet.

    ```java
    return cluster.connect();
    ```

A következő kódrészletek a *src/Main/Java/com/Azure/cosmosdb/Cassandra/adattár/UserRepository. Java* fájlból származnak.

* Új kulcsterület létrehozása.

    ```java
    public void createKeyspace() {
        final String query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {'class': 'SimpleStrategy', 'replication_factor': '3' } ";
        session.execute(query);
        LOGGER.info("Created keyspace 'uprofile'");
    }
    ```

* Új tábla létrehozása.

   ```java
   public void createTable() {
        final String query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        session.execute(query);
        LOGGER.info("Created table 'user'");
   }
   ```

* Felhasználói entitások beszúrása előkészített utasításobjektum használatával.

    ```java
    public PreparedStatement prepareInsertStatement() {
        final String insertStatement = "INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (?,?,?)";
        return session.prepare(insertStatement);
    }

    public void insertUser(PreparedStatement statement, int id, String name, String city) {
        BoundStatement boundStatement = new BoundStatement(statement);
        session.execute(boundStatement.bind(id, name, city));
    }
    ```

* Összes felhasználói adat lekérdezése.

    ```java
   public void selectAllUsers() {
        final String query = "SELECT * FROM uprofile.user";
        List<Row> rows = session.execute(query).all();

        for (Row row : rows) {
            LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
        }
    }
    ```

* Egyetlen felhasználó adatainak lekérdezése.

    ```java
    public void selectUser(int id) {
        final String query = "SELECT * FROM uprofile.user where user_id = 3";
        Row row = session.execute(query).one();

        LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
    }
    ```

## <a name="update-your-connection-string"></a>A kapcsolati sztring frissítése

Lépjen vissza az Azure Portalra a kapcsolati sztring adataiért, majd másolja be azokat az alkalmazásba. A kapcsolati sztring részletei lehetővé teszik az alkalmazás számára, hogy kommunikáljon az üzemeltetett adatbázissal.

1. A [Azure Portal](https://portal.azure.com/)Azure Cosmos db-fiókjában válassza a **kapcsolatok karakterlánc** lehetőséget. 

    :::image type="content" source="./media/create-cassandra-java/copy-username-connection-string-azure-portal.png" alt-text="Felhasználónév megtekintése és másolás az Azure Portal Kapcsolati sztring oldaláról":::

2. A :::image type="icon" source="./media/create-cassandra-java/copy-button-azure-portal.png"::: kapcsolódási pont értékének másolásához használja a képernyő jobb oldalán található gombot. 

3. Nyissa meg a *config. properties* fájlt a *C:\git-samples\azure-cosmosdb-Cassandra-Java-Getting-started\java-examples\src\main\resources* mappából. 

3. Illessze be a CONTACT POINT értéket a Portalból a `<Cassandra endpoint host>` helyére a 2. sorban.

    A *config. properties fájl 2.* sora a következőhöz hasonlóan néz ki: 

    `cassandra_host=cosmos-db-quickstart.cassandra.cosmosdb.azure.com`

3. Lépjen vissza a portálra, és másolja a USERNAME értéket. Illessze be a USERNAME értéket a Portalból a 4. sorban található `<cassandra endpoint username>` érték helyére.

    A *config. properties fájl 4.* sora ekkor a következőhöz hasonlóan néz ki: 

    `cassandra_username=cosmos-db-quickstart`

4. Lépjen vissza a portálra, és másolja a jelszó értékét. Illessze be a PASSWORD értéket a Portalból az 5. sorban található `<cassandra endpoint password>` érték helyére.

    A *config. properties 5.* sora ekkor a következőhöz hasonlóan néz ki: 

    `cassandra_password=2Ggkr662ifxz2Mg...==`

5. Ha a 6. sorban egy adott TLS/SSL-tanúsítványt szeretne használni, akkor a helyére írja be a `<SSL key store file location>` TLS/SSL-tanúsítvány helyét. Ha nem ad meg semmilyen értéket, a rendszer a <JAVA_HOME>/jre/lib/security/cacerts helyen telepített JDK-tanúsítványt használja. 

6. Ha a 6. sort egy adott TLS/SSL-tanúsítvány használatára módosította, frissítse a 7. sort a tanúsítvány jelszavának használatára. 

7. Mentse a *config. properties* fájlt.

## <a name="run-the-java-app"></a>A Java-alkalmazás futtatása

1. A git terminálablakában a `cd` paranccsal lépjen az `azure-cosmosdb-cassandra-java-getting-started` mappába.

    ```git
    cd "C:\git-samples\azure-cosmosdb-cassandra-java-getting-started"
    ```

2. A git terminálablakban használja a következő parancsot a `cosmosdb-cassandra-examples.jar` fájl létrehozásához.

    ```git
    mvn clean install
    ```

3. A git terminálablakban futtassa a következő parancsot a Java-alkalmazás indításához.

    ```git
    java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
    ```

    A terminálablak értesítéseket jelenít meg a kulcstér és a tábla létrehozásáról. Ezt követően kiválasztja és visszaadja a táblában található összes felhasználót, és megjeleníti a kimenetet, majd azonosító alapján kiválaszt egy sort, és megjeleníti az értéket.  

    Nyomja le a CTRL + C billentyűkombinációt a program végrehajtásának leállításához és a konzol ablak bezárásához.

4. Ha megnyitja az **Adatkezelőt** az Azure Portalon, lekérdezheti és módosíthatja és használhatja az új adatokat. 

    :::image type="content" source="./media/create-cassandra-java/view-data-explorer-java-app.png" alt-text="Adatkezelő-Azure Cosmos DBban lévő adatmegjelenítés":::

## <a name="review-slas-in-the-azure-portal"></a>Tekintse át az SLA-kat az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

Ebből a rövid útmutatóból megtudhatta, hogyan hozhat létre egy Azure Cosmos DB fiókot a Cassandra API, és hogyan futtathat egy Cassandra Java-alkalmazást, amely létrehoz egy Cassandra-adatbázist és-tárolót. Mostantól további adatait is importálhatja a Azure Cosmos DB-fiókjába. 

> [!div class="nextstepaction"]
> [Cassandra-adatok importálása az Azure Cosmos DB-be](cassandra-import-data.md)
