---
title: Azure-gyorsítótár kötése az Azure Spring Cloud-alkalmazás Redis
description: Ismerje meg, hogyan köthető az Azure cache a Redis Azure Spring Cloud-alkalmazásához
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/31/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 414f7dc64027b286d8a963452d2b86126b3c5818
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878132"
---
# <a name="bind-azure-cache-for-redis-to-your-azure-spring-cloud-application"></a>Azure-gyorsítótár kötése az Azure Spring Cloud-alkalmazás Redis 

**Ez a cikk a következőkre vonatkozik:** ✔️ Java

A Spring boot-alkalmazások manuális konfigurálása helyett az Azure Spring Cloud használatával automatikusan köthető az Azure-szolgáltatások kiválasztásához az alkalmazásaihoz. Ez a cikk bemutatja, hogyan köthető az alkalmazás az Azure cache-hez a Redis.

## <a name="prerequisites"></a>Előfeltételek

* Üzembe helyezett Azure Spring Cloud-példány
* Azure cache a Redis Service-példányhoz
* Azure Spring Cloud-bővítmény az Azure CLI-hez

Ha nem rendelkezik telepített Azure Spring Cloud-példánnyal, kövesse az [Azure Spring Cloud-alkalmazás üzembe helyezésének](spring-cloud-quickstart.md)rövid útmutatójában ismertetett lépéseket.

## <a name="bind-azure-cache-for-redis"></a>Az Azure cache kötése a Redis-hez

1. Adja hozzá a következő függőséget a projekt pom.xml fájljához:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. `spring.redis.*`A fájl összes tulajdonságának `application.properties` eltávolítása

1. Frissítse az aktuális telepítést a használatával `az spring-cloud app update` , vagy hozzon létre egy új központi telepítést a használatával `az spring-cloud app deployment create` .

1. Lépjen a Azure Portal Azure Spring Cloud Service oldalára. Nyissa meg az **alkalmazás irányítópultját** , és válassza ki azt az alkalmazást, amelyet az Azure cache-hez szeretne kötni a Redis. Ez az alkalmazás ugyanaz, mint amelyet az előző lépésben frissített vagy telepített.

1. Válassza ki a **szolgáltatási kötés** elemet, majd válassza a **szolgáltatás kötésének létrehozása** lehetőséget. Töltse **ki az űrlapot** , és ügyeljen arra, hogy válassza ki az **Azure cache Redis**, a Redis-kiszolgáló Azure-gyorsítótárát és az **elsődleges** kulcs beállítást.

1. Indítsa újra az alkalmazást. A kötésnek most már működnie kell.

1. A szolgáltatási kötés helyességének biztosításához válassza ki a kötés nevét, és ellenőrizze annak részleteit. A `property` mezőnek így kell kinéznie:
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan köthető az Azure Spring Cloud-alkalmazás az Azure cache Redis-hez. Ha többet szeretne megtudni az alkalmazáshoz tartozó kötési szolgáltatásokról, tekintse meg a [kötés egy Azure Database for MySQL-példányhoz](spring-cloud-howto-bind-mysql.md)című témakört.
