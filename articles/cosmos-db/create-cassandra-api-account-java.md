---
title: 'Oktatóanyag: Java-alkalmazás létrehozása Azure Cosmos DB Cassandra API-fiók létrehozásához'
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Cassandra API fiókot, hogyan adhat hozzá egy adatbázist (más néven helyigényt), és hogyan adhat hozzá egy táblát a fiókhoz Java-alkalmazás használatával.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/06/2018
ms.custom: seodec18, devx-track-java
ms.openlocfilehash: ca1fdbd9aa2c98358489d91fe0839c98adec293b
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102212705"
---
# <a name="tutorial-create-a-cassandra-api-account-in-azure-cosmos-db-by-using-a-java-application-to-store-keyvalue-data"></a>Oktatóanyag: Cassandra API-fiók létrehozása a Azure Cosmos DBban Java-alkalmazás használatával a kulcs/érték típusú adattároláshoz
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Fejlesztőként lehetnek olyan alkalmazások, amelyek kulcs/érték párokat használnak. A kulcs/érték típusú adattároláshoz a Azure Cosmos DB Cassandra API fiókját is használhatja. Ez az oktatóanyag azt ismerteti, hogyan használható egy Java-alkalmazás egy Cassandra API-fiók létrehozásához a Azure Cosmos DBban, egy adatbázis (más néven szóköz) hozzáadását és egy tábla hozzáadását. A Java-alkalmazás a [Java-illesztőprogrammal](https://github.com/datastax/java-driver) hoz létre egy olyan felhasználói adatbázist, amely olyan adatokat tartalmaz, mint például a felhasználói azonosító, a Felhasználónév és a felhasználói város.  

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Cassandra-adatbázisfiók létrehozása
> * A fiók kapcsolati sztringjének lekérése
> * Maven-projekt és-függőségek létrehozása
> * Egy adatbázis és egy tábla hozzáadása
> * Az alkalmazás futtatása

## <a name="prerequisites"></a>Előfeltételek 

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a virtuális gép létrehozásának megkezdése előtt. 

* Szerezze be a [Java Development Kit (JDK)](/java/azure/jdk/)legújabb verzióját. 

* [Töltse le](https://maven.apache.org/download.cgi) és [telepítse](https://maven.apache.org/install.html) a [Maven](https://maven.apache.org/) bináris archívumát. 
  - Ubuntu rendszeren futtathatja az `apt-get install maven` parancsot a Maven telepítéséhez. 

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/). 

2. Válassza **az erőforrás létrehozása**  >  **adatbázisok**  >  **Azure Cosmos db** elemet. 

3. Az **új fiók** panelen adja meg az új Azure Cosmos-fiók beállításait. 

   |Beállítás   |Ajánlott érték  |Leírás  |
   |---------|---------|---------|
   |ID (Azonosító)   |   Adjon meg egy egyedi nevet    | Adjon meg egy egyedi nevet az Azure Cosmos-fiók azonosításához. <br/><br/>A rendszer a .azure.com utótaggal egészíti ki a megadott azonosítót a kapcsolódási pont létrehozásához, ezért válasszon egyedi, de felismerhető azonosítót.         |
   |API    |  Cassandra   |  A létrehozni kívánt fiók típusát az API határozza meg. <br/> Válassza a **Cassandra** lehetőséget, mert ebben a cikkben egy olyan széles oszlopú adatbázist fog létrehozni, amely a Cassandra Query Language (CQL) szintaxis használatával kérdezhető le.  |
   |Előfizetés    |  Az Ön előfizetése        |  Válassza ki az Azure Cosmos-fiókhoz használni kívánt Azure-előfizetést.        |
   |Erőforráscsoport   | Adjon meg egy nevet    |  Válassza az **Új létrehozása** elemet, majd adja meg a fiók új erőforráscsoport-nevét. Az egyszerűség kedvéért használhat az azonosítójával megegyező nevet.    |
   |Hely    |  Válassza ki a felhasználóihoz legközelebb eső régiót    |  Válassza ki azt a földrajzi helyet, ahol az Azure Cosmos-fiókját tárolni szeretné. Használja a felhasználókhoz legközelebb eső helyet, hogy a lehető leggyorsabb hozzáférést biztosítsa az adataihoz.    |

   :::image type="content" source="./media/create-cassandra-api-account-java/create-account.png" alt-text="Fiók létrehozása a portál használatával":::

4. Válassza a **Létrehozás** lehetőséget. <br/>A fiók létrehozása eltarthat néhány percig. Az erőforrás létrehozása után az **üzembe helyezés sikeres** értesítése látható a portál jobb oldalán.

## <a name="get-the-connection-details-of-your-account"></a>A fiók kapcsolati adatainak lekérése  

Szerezze be a Azure Portalről a kapcsolatok karakterláncának adatait, és másolja be a Java-konfigurációs fájlba. A kapcsolati sztring lehetővé teszi az alkalmazás számára, hogy kommunikáljon az üzemeltetett adatbázissal. 

1. A [Azure Portal](https://portal.azure.com/)lépjen az Azure Cosmos-fiókjába. 

2. Nyissa meg a **kapcsolatok karakterlánca** panelt.  

3. Másolja ki a **CONTACT POINT**, **PORT**, **USERNAME** és **PRIMARY PASSWORD** értékeket, amelyekre a következő lépések során lesz szüksége.

## <a name="create-the-project-and-the-dependencies"></a>A projekt és a függőségek létrehozása 

A cikkben használt Java-minta projekt a GitHubban található. Futtathatja a jelen dokumentum lépéseit, vagy letöltheti a mintát az [Azure-Cosmos-db-Cassandra-Java-Getting-Started](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started) adattárból. 

A fájlok letöltése után frissítse a kapcsolatok karakterlánc-információit a `java-examples\src\main\resources\config.properties` fájlon belül, és futtassa azt.  

```java
cassandra_host=<FILLME_with_CONTACT POINT> 
cassandra_port = 10350 
cassandra_username=<FILLME_with_USERNAME> 
cassandra_password=<FILLME_with_PRIMARY PASSWORD> 
```

A következő lépésekkel hozhatja létre a mintát a semmiből: 

1. A terminálból vagy parancssorból hozzon létre egy Cassandra-demo nevű új Maven-projektet. 

   ```bash
   mvn archetype:generate -DgroupId=com.azure.cosmosdb.cassandra -DartifactId=cassandra-demo -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false 
   ```
 
2. Keresse meg a `cassandra-demo` mappát. Egy szövegszerkesztővel nyissa meg a létrejött `pom.xml` fájlt. 

   Adja hozzá a projekthez szükséges Cassandra-függőségeket és a Build beépülő modult, ahogyan az a [pom.xml](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/pom.xml) fájlban látható.  

3. A `cassandra-demo\src\main` mappában hozzon létre egy új mappát `resources` néven.  Az erőforrások mappájában adja hozzá a config.properties és log4j.properties fájlokat:

   - A [config. properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/src/main/resources/config.properties) fájl tárolja a Cassandra API fiókhoz tartozó kapcsolatok végpontját és kulcsának értékét. 
   
   - A [log4j. properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/src/main/resources/log4j.properties) fájl határozza meg, hogy milyen szintű naplózásra van szükség a Cassandra API való interakcióhoz.  

4. Tallózással keresse meg a `src/main/java/com/azure/cosmosdb/cassandra/` mappát. A cassandra mappán belül hozzon létre egy másik mappát `utils` néven. Ez az új mappa tartalmazza a Cassandra API-fiókhoz való csatlakozáshoz szükséges segédeszközosztályokat. 

   Adja hozzá a [CassandraUtils](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java) osztályt a fürt létrehozásához és a Cassandra-munkamenetek megnyitásához és bezárásához. A fürt csatlakozik a Azure Cosmos DB Cassandra API-fiókjához, és visszaadja az elérni kívánt munkamenetet. Használja a [Configurations](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/src/main/java/com/azure/cosmosdb/cassandra/util/Configurations.java) osztályt a kapcsolati sztring adatainak olvasásához a config.properties fájlból. 

5. A Java-minta olyan adatbázist hoz létre a felhasználói adatokkal, mint a Felhasználónév, a felhasználói azonosító és a User City. Meg kell adnia a get és set metódusokat a fő függvényben található felhasználói adatok eléréséhez.
 
   Hozzon létre egy [User. Java](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/src/main/java/com/azure/cosmosdb/cassandra/examples/UserProfile.java) osztályt a `src/main/java/com/azure/cosmosdb/cassandra/` mappában a Get és a set metódussal. 

## <a name="add-a-database-and-a-table"></a>Egy adatbázis és egy tábla hozzáadása  

Ez a szakasz azt ismerteti, hogyan adhat hozzá egy adatbázist (szóköz) és egy táblázatot a CQL használatával.

1. A `src\main\java\com\azure\cosmosdb\cassandra` mappában hozzon létre egy új mappát `repository` néven. 

2. Hozza létre a `UserRepository` Java-osztályt, és adja hozzá a következő kódot: 

   ```java
   package com.azure.cosmosdb.cassandra.repository; 
   import java.util.List; 
   import com.datastax.driver.core.BoundStatement; 
   import com.datastax.driver.core.PreparedStatement; 
   import com.datastax.driver.core.Row; 
   import com.datastax.driver.core.Session; 
   import org.slf4j.Logger; 
   import org.slf4j.LoggerFactory; 
   
   /** 
    * Create a Cassandra session 
    */ 
   public class UserRepository { 
   
       private static final Logger LOGGER = LoggerFactory.getLogger(UserRepository.class); 
       private Session session; 
       public UserRepository(Session session) { 
           this.session = session; 
       } 
   
       /** 
       * Create keyspace uprofile in cassandra DB 
        */ 
   
       public void createKeyspace() { 
            final String query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 }"; 
           session.execute(query); 
           LOGGER.info("Created keyspace 'uprofile'"); 
       } 
   
       /** 
        * Create user table in cassandra DB 
        */ 
   
       public void createTable() { 
           final String query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)"; 
           session.execute(query); 
           LOGGER.info("Created table 'user'"); 
       } 
   } 
   ```

3. Lépjen az `src\main\java\com\azure\cosmosdb\cassandra` mappába, és hozzon létre egy új almappát `examples` néven.

4. Hozza létre a `UserProfile` Java-osztályt. Ez az osztály tartalmazza a fő metódust, amely meghívja a korábban definiált createKeyspace és createTable metódusokat: 

   ```java
   package com.azure.cosmosdb.cassandra.examples; 
   import java.io.IOException; 
   import com.azure.cosmosdb.cassandra.repository.UserRepository; 
   import com.azure.cosmosdb.cassandra.util.CassandraUtils; 
   import com.datastax.driver.core.PreparedStatement; 
   import com.datastax.driver.core.Session; 
   import org.slf4j.Logger; 
   import org.slf4j.LoggerFactory; 
   
   /** 
    * Example class which will demonstrate following operations on Cassandra Database on CosmosDB 
    * - Create Keyspace 
    * - Create Table 
    * - Insert Rows 
    * - Select all data from a table 
    * - Select a row from a table 
    */ 
   
   public class UserProfile { 
   
       private static final Logger LOGGER = LoggerFactory.getLogger(UserProfile.class); 
       public static void main(String[] s) throws Exception { 
           CassandraUtils utils = new CassandraUtils(); 
           Session cassandraSession = utils.getSession(); 
   
           try { 
               UserRepository repository = new UserRepository(cassandraSession); 
               //Create keyspace in cassandra database 
               repository.createKeyspace(); 
               //Create table in cassandra database 
               repository.createTable(); 
   
           } finally { 
               utils.close(); 
               LOGGER.info("Please delete your table after verifying the presence of the data in portal or from CQL"); 
           } 
       } 
   } 
   ```
 
## <a name="run-the-app"></a>Az alkalmazás futtatása 

1. Nyisson meg egy parancssort vagy terminálablakot. Illessze be az alábbi kódblokkot. 

   Ez a kód módosítja a könyvtárat (CD) arra a mappára, ahová a projektet létrehozta. Ezután futtatja az `mvn clean install` parancsot a `cosmosdb-cassandra-examples.jar` fájl célmappán belüli létrehozásához. Végül futtatja a Java-alkalmazást.

   ```bash
   cd cassandra-demo

   mvn clean install 

   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile 
   ```

   A terminálablak értesítéseket jelenít meg a kulcstér és a tábla létrehozásáról. 
   
2. Most nyissa meg az Azure Portalon az **Adatkezelőt**, és ellenőrizze, hogy a kulcstér és a tábla létrejött-e.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan hozhat létre Cassandra API fiókot a Azure Cosmos DBban, egy adatbázisban és egy táblában egy Java-alkalmazás használatával. Továbbléphet a következő cikkre:

> [!div class="nextstepaction"]
> [mintaadatok betöltése a Cassandra API-táblába](cassandra-api-load-data.md).
