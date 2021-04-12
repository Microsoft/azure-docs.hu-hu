---
title: Azure Database for MySQL-példány kötése az Azure Spring Cloud-alkalmazáshoz
description: Megtudhatja, hogyan köthető egy Azure Database for MySQL-példány az Azure Spring Cloud-alkalmazáshoz
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: cc1c186f9acb6d49314f5d581a4b51ffe49d6627
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878052"
---
# <a name="bind-an-azure-database-for-mysql-instance-to-your-azure-spring-cloud-application"></a>Azure Database for MySQL-példány kötése az Azure Spring Cloud-alkalmazáshoz 

**Ez a cikk a következőkre vonatkozik:** ✔️ Java

Az Azure Spring Cloud-ban az Azure-szolgáltatások automatikus kiválasztását is elvégezheti az alkalmazásaiban, ahelyett, hogy a Spring boot-alkalmazást manuálisan kellene konfigurálnia. Ez a cikk bemutatja, hogyan köthető az alkalmazás a Azure Database for MySQL-példányhoz.

## <a name="prerequisites"></a>Előfeltételek

* Üzembe helyezett Azure Spring Cloud-példány
* Egy Azure Database for MySQL fiók
* Azure CLI

Ha nem rendelkezik telepített Azure Spring Cloud-példánnyal, kövesse a gyors útmutató [: Azure Spring Cloud-alkalmazás elindítása a Azure Portal használatával](spring-cloud-quickstart.md) az első rugós felhőalapú alkalmazás üzembe helyezéséhez.

## <a name="bind-your-app-to-your-azure-database-for-mysql-instance"></a>Az alkalmazás kötése az Azure Database for MySQL-példányhoz

1. Jegyezze fel a Azure Database for MySQL fiókjának rendszergazdai felhasználónevét és jelszavát. 

1. Kapcsolódjon a kiszolgálóhoz, hozzon létre egy **testdb** nevű adatbázist egy MySQL-ügyfélről, majd hozzon létre egy új, nem rendszergazdai fiókot.

1. A projekt *pom.xml* fájljában adja hozzá a következő függőséget:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```
1. Az *Application. properties* fájlban távolítsa el a `spring.datasource.*` tulajdonságokat.

1. Frissítse az aktuális telepítést a futtatásával `az spring-cloud app update` , vagy hozzon létre egy új központi telepítést ehhez a változáshoz a futtatásával `az spring-cloud app deployment create` .  Ezek a parancsok vagy az új függőséggel frissítik vagy létrehozzák az alkalmazást.

1. A Azure Portal az **Azure Spring Cloud** Service oldalon keresse meg az **alkalmazás irányítópultját**, majd válassza ki az alkalmazást, amelyet a Azure Database for MySQL-példányhoz szeretne kötni.  Ez ugyanaz az alkalmazás, amelyet az előző lépésben frissített vagy telepített. 

1. Válassza ki a **szolgáltatási kötés** elemet, majd kattintson a **szolgáltatás kötésének létrehozása** gombra. 

1. Töltse ki az űrlapot, és válassza az **Azure MySQL** -t a **kötés típusaként**, és használja ugyanazt az adatbázisnevet, amelyet korábban használt, és használja ugyanazt a felhasználónevet és jelszót, amelyet az első lépésben feljegyzett.

1. Indítsa újra az alkalmazást, és ez a kötés most már működnie kell.

1. A szolgáltatási kötés helyességének biztosításához jelölje ki a kötés nevét, majd ellenőrizze a részleteit. A `property` mezőnek így kell kinéznie:
    ```
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan köthető egy Azure Spring Cloud-alkalmazás egy Azure Database for MySQL-példányhoz. Ha többet szeretne megtudni az alkalmazások kötési szolgáltatásairól, olvassa el a [Azure Cosmos db adatbázis kötése Azure Spring Cloud-alkalmazáshoz](spring-cloud-howto-bind-cosmos.md)című témakört.
