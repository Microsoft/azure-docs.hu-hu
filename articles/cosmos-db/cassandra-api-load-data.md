---
title: 'Oktatóanyag: Java-alkalmazás, amely a mintaadatok betöltését Cassandra API táblázatba Azure Cosmos DB'
description: Ez az oktatóanyag bemutatja, hogyan tölthetők be a minta felhasználói adatai egy Cassandra API-táblába Azure Cosmos DB egy Java-alkalmazás használatával.
author: kanshiG
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: ad3dfc8c86d22e58377ebfbdb94b2beb004ded56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93092466"
---
# <a name="tutorial-load-sample-data-into-a-cassandra-api-table-in-azure-cosmos-db"></a>Oktatóanyag: mintaadatok betöltése egy Cassandra API-táblába Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Fejlesztőként lehetnek olyan alkalmazások, amelyek kulcs/érték párokat használnak. A kulcs/érték típusú adathalmazok tárolásához és kezeléséhez használhatja Azure Cosmos DB Cassandra API fiókját. Ez az oktatóanyag bemutatja, hogyan tölthetők be a minta felhasználói adatai egy Cassandra API-fiókba Azure Cosmos DB egy Java-alkalmazás használatával. A Java-alkalmazás a [Java-illesztőprogramot](https://github.com/datastax/java-driver) használja, és betölti a felhasználói adatértékeket, például a felhasználói azonosítót, a felhasználónevet és a felhasználói várost. 

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Adatgyűjtés Cassandra-táblába
> * Az alkalmazás futtatása

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek

* Ez a cikk egy többrészes oktatóanyag része. Mielőtt elkezdené ezt a dokumentumot, [hozza létre a Cassandra API fiókot, a térközt és a táblát](create-cassandra-api-account-java.md).   

## <a name="load-data-into-the-table"></a>Adatok betöltése a táblába

Az adatok Cassandra API-táblába történő betöltéséhez hajtsa végre a következő lépéseket:

1. Nyissa meg a "src\main\java\com\azure\cosmosdb\cassandra" mappában található "UserRepository. Java" fájlt, és fűzze hozzá a kódot, hogy beszúrja a user_id, user_name és user_bcity mezőket a táblába:

   ```java
   /**
   * Insert a row into user table
   *
   * @param id   user_id
   * @param name user_name
   * @param city user_bcity
   */
   public void insertUser(PreparedStatement statement, int id, String name, String city) {
        BoundStatement boundStatement = new BoundStatement(statement);
        session.execute(boundStatement.bind(id, name, city));
   }

   /**
   * Create a PrepareStatement to insert a row to user table
   *
   * @return PreparedStatement
   */
   public PreparedStatement prepareInsertStatement() {
      final String insertStatement = "INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (?,?,?)";
   return session.prepare(insertStatement);
   }
   ```
 
2. Nyissa meg a "src\main\java\com\azure\cosmosdb\cassandra" mappában található "Profile. Java" fájlt. Ez az osztály tartalmazza a fő metódust, amely meghívja a korábban definiált createKeyspace és createTable metódusokat. Ezután fűzze hozzá a következő kódot, amely mintaadatokat illeszt a Cassandra API-táblába.

   ```java
   //Insert rows into user table
   PreparedStatement preparedStatement = repository.prepareInsertStatement();
     repository.insertUser(preparedStatement, 1, "JohnH", "Seattle");
     repository.insertUser(preparedStatement, 2, "EricK", "Spokane");
     repository.insertUser(preparedStatement, 3, "MatthewP", "Tacoma");
     repository.insertUser(preparedStatement, 4, "DavidA", "Renton");
     repository.insertUser(preparedStatement, 5, "PeterS", "Everett");
   ```

## <a name="run-the-app"></a>Az alkalmazás futtatása

Nyisson meg egy parancssort vagy egy terminált, és módosítsa a mappa elérési útját a projekt létrehozásához. Futtassa a "MVN tiszta telepítés" parancsot a cosmosdb-Cassandra-examples. jar fájl létrehozásához a célmappán belül, és futtassa az alkalmazást. 

```bash
cd "cassandra-demo"

mvn clean install

java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
```

Ezután megnyithatja az Adatkezelőt az Azure Portalon, hogy ellenőrizze, hozzá lettek-e adva a felhasználói adatok a táblához.
    
## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan tölthető be a mintaadatok egy Cassandra API-fiókba Azure Cosmos DB-ban. Továbbléphet a következő cikkre:

> [!div class="nextstepaction"]
> [Adatok lekérdezése egy Cassandra API-fiókból](cassandra-api-query-data.md)
