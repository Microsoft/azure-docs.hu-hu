---
title: Oktatóanyag – Azure Cosmos DB kötése az Azure Spring Cloud-alkalmazáshoz
description: Ismerje meg, hogyan köthető Azure Cosmos DB az Azure Spring Cloud-alkalmazáshoz
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/06/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: e3af90eb00bd3f02fbcc3e59d2fa6ee5a1b3a9e8
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878013"
---
# <a name="tutorial-bind-an-azure-cosmos-db-database-to-your-azure-spring-cloud-application"></a>Oktatóanyag: Azure Cosmos DB-adatbázis kötése az Azure Spring Cloud-alkalmazáshoz

**Ez a cikk a következőkre vonatkozik:** ✔️ Java

A Spring boot-alkalmazások manuális konfigurálása helyett az Azure Spring Cloud használatával automatikusan köthető az Azure-szolgáltatások kiválasztásához az alkalmazásaihoz. Ez a cikk bemutatja, hogyan köthető az alkalmazás egy Azure Cosmos DB adatbázishoz.

Előfeltételek:

* Egy üzembe helyezett Azure Spring Cloud-példány. Az első lépésekhez kövesse [Az Azure CLI-n keresztül történő üzembe helyezést ismertető](spring-cloud-quickstart.md) útmutatót.
* Egy Azure Cosmos DB fiók, amelynek a minimális jogosultsági szintje közreműködő.

## <a name="bind-azure-cosmos-db"></a>Kötés Azure Cosmos DB

Azure Cosmos DB öt különböző API-típust támogat, amelyek támogatják a kötést. A következő eljárás azt szemlélteti, hogyan használhatók:

1. Létrehoz egy Azure Cosmos DB-adatbázist. Az [adatbázis létrehozásával](../cosmos-db/create-cosmosdb-resources-portal.md) kapcsolatos útmutatót a súgóban találja. 

1. Jegyezze fel az adatbázis nevét. Ehhez az eljáráshoz az adatbázis neve **testdb**.

1. Adja hozzá az alábbi függőségek egyikét az Azure Spring Cloud-alkalmazás pom.xml-fájljához. Válassza ki az API-típusnak megfelelő függőséget.

    * API típusa: Core (SQL)

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
          <version>2.1.6</version>
      </dependency>
      ```

    * API típusa: MongoDB

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-mongodb</artifactId>
      </dependency>
      ```

    * API-típus: Cassandra

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-cassandra</artifactId>
      </dependency>
      ```

    * API-típus: Gremlin (gráf)

      ```xml
      <dependency>
          <groupId>com.microsoft.spring.data.gremlin</groupId>
          <artifactId>spring-data-gremlin</artifactId>
          <version>2.1.7</version>
      </dependency>
      ```

    * API-típus: Azure-tábla

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-storage-spring-boot-starter</artifactId>
          <version>2.0.5</version>
      </dependency>
      ```

1. A használatával `az spring-cloud app update` frissítheti az aktuális telepítést, vagy a használatával `az spring-cloud app deployment create` új központi telepítést hozhat létre. Ezek a parancsok vagy az új függőséggel frissítik vagy létrehozzák az alkalmazást.

1. Lépjen a Azure Portal Azure Spring Cloud Service oldalára. Nyissa meg az **alkalmazás irányítópultját** , és válassza ki a Azure Cosmos DBhoz kötni kívánt alkalmazást. Ez az alkalmazás ugyanaz, mint amelyet az előző lépésben frissített vagy telepített.

1. Válassza ki a **szolgáltatási kötés** elemet, majd válassza a **szolgáltatás kötésének létrehozása** lehetőséget. Az űrlap kitöltéséhez válassza a következő lehetőséget:
   * A **kötési típus** értéke **Azure Cosmos db**.
   * Az API-típus.
   * Az adatbázis neve.
   * A Azure Cosmos DB fiók.

    > [!NOTE]
    > Ha Cassandra-t használ, az adatbázis nevének egyik kulcsát kell használnia.

1. Indítsa újra az alkalmazást az alkalmazás lapon az **Újraindítás** lehetőség kiválasztásával.

1. A szolgáltatás megfelelő kötésének biztosításához válassza ki a kötés nevét, és ellenőrizze annak részleteit. `property`Ennek a példának a következőhöz hasonlónak kell lennie:

    ```
    azure.cosmosdb.uri=https://<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan köthető az Azure Spring Cloud-alkalmazás egy Azure Cosmos DB adatbázishoz. Ha többet szeretne megtudni az alkalmazásra vonatkozó kötési szolgáltatásokról, tekintse meg a [kötés egy Azure cache-hez a Redis cache-hez](spring-cloud-howto-bind-redis.md)című témakört.