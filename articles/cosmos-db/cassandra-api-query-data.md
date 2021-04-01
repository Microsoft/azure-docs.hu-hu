---
title: 'Oktatóanyag: adatok lekérdezése Cassandra API-fiókból Azure Cosmos DB'
description: Ez az oktatóanyag bemutatja, hogyan kérdezheti le a felhasználói adatait egy Azure Cosmos DB Cassandra API-fiókból egy Java-alkalmazás használatával.
ms.service: cosmos-db
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: 693984ceaef1a71ebfbe4122c18a45a563c9c101
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93100433"
---
# <a name="tutorial-query-data-from-a-cassandra-api-account-in-azure-cosmos-db"></a>Oktatóanyag: adatok lekérdezése Cassandra API-fiókból Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Fejlesztőként lehetnek olyan alkalmazások, amelyek kulcs/érték párokat használnak. A kulcs/érték típusú adattárolók tárolására és lekérdezésére Cassandra API fiókot használhat a Azure Cosmos DBban. Ez az oktatóanyag azt mutatja be, hogyan lehet lekérdezni a felhasználói adatait egy Cassandra API-fiókból a Azure Cosmos DB Java-alkalmazás használatával. A Java-alkalmazás a [Java-illesztőprogramot](https://github.com/datastax/java-driver) használja, és lekérdezi a felhasználói adatforrásokat, például a felhasználói azonosítót, a felhasználónevet és a felhasználói város nevét. 

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Adatok lekérdezése Cassandra-táblából
> * Az alkalmazás futtatása

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek

* Ez a cikk egy többrészes oktatóanyag része. Mielőtt elkezdené, győződjön meg arról, hogy az előző lépések végrehajtásával hozza létre a Cassandra API fiókot, a térközt, a táblát és [a betöltési mintát a táblába](cassandra-api-load-data.md). 

## <a name="query-data"></a>Adatok lekérdezése

Az alábbi lépéseket követve kérdezze le a Cassandra API-fiók adatait:

1. Nyissa meg az`src\main\java\com\azure\cosmosdb\cassandra` mappában lévő `UserRepository.java` fájlt. Fűzze hozzá a fájlhoz a következő kódblokkot. Ez a kód a következő három metódust biztosítja: 

   * Az adatbázis összes felhasználójának lekérdezése
   * Felhasználói azonosító alapján szűrt konkrét felhasználó lekérdezése
   * Tábla törlése

   ```java
   /**
   * Select all rows from user table
   */
   public void selectAllUsers() {

     final String query = "SELECT * FROM uprofile.user";
     List<Row> rows = session.execute(query).all();

     for (Row row : rows) {
        LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
     }
   }

   /**
   * Select a row from user table
   *
   * @param id user_id
   */
   public void selectUser(int id) {
      final String query = "SELECT * FROM uprofile.user where user_id = 3";
      Row row = session.execute(query).one();

      LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
   }

   /**
   * Delete user table.
   */
   public void deleteTable() {
     final String query = "DROP TABLE IF EXISTS uprofile.user";
     session.execute(query);
   }
   ```

2. Nyissa meg az`src\main\java\com\azure\cosmosdb\cassandra` mappában lévő `UserProfile.java` fájlt. Ez az osztály tartalmazza a createKeyspace és a createTable parancsokat meghívó fő metódust. Szúrja be a korábban meghatározott adatmetódusokat. Most fűzze hozzá a következő kódot, amely lekérdezi az összes felhasználót vagy egy adott felhasználót:

   ```java
   LOGGER.info("Select all users");
   repository.selectAllUsers();

   LOGGER.info("Select a user by id (3)");
   repository.selectUser(3);

   LOGGER.info("Delete the users profile table");
   repository.deleteTable();
   ```

## <a name="run-the-java-app"></a>A Java-alkalmazás futtatása
1. Nyisson meg egy parancssort vagy terminálablakot. Illessze be az alábbi kódblokkot. 

   Ez a kód módosítja a könyvtárat (CD) arra a mappára, ahová a projektet létrehozta. Ezután futtatja az `mvn clean install` parancsot a `cosmosdb-cassandra-examples.jar` fájl célmappán belüli létrehozásához. Végül futtatja a Java-alkalmazást.

   ```bash
   cd "cassandra-demo"
   
   mvn clean install
   
   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
   ```

2. Nyissa meg az Azure Portalon az **Adatkezelőt**, és ellenőrizze, hogy a felhasználói tábla törlődött-e.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölheti az erőforráscsoportot, az Azure Cosmos-fiókot és az összes kapcsolódó erőforrást. Ehhez válassza ki a virtuális géphez tartozó erőforráscsoportot, válassza a **Törlés** lehetőséget, majd erősítse meg a törölni kívánt erőforráscsoport nevét.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan lehet lekérdezni egy Cassandra API fiók adatait a Azure Cosmos DBban. Továbbléphet a következő cikkre:

> [!div class="nextstepaction"]
> [Adatok áttelepítése Cassandra API-fiókba](cassandra-import-data.md)


