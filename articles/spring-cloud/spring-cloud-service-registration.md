---
title: A szolgáltatás beállításjegyzékének és felderítésének automatizálása
description: Ismerje meg, hogyan automatizálható a szolgáltatás-felderítés és-regisztráció a Spring Cloud Service beállításjegyzék használatával
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: c3e26b157630df6004292c93a0a0a47307d5949a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87071022"
---
# <a name="discover-and-register-your-spring-cloud-services"></a>A Spring Cloud Services felderítése és regisztrálása

A Service Discovery kulcsfontosságú követelmény a Service-alapú architektúrák számára.  Az egyes ügyfelek manuális beállítása időt vesz igénybe, és bevezeti az emberi hiba lehetőségét.  Az Azure Spring Cloud Service beállításjegyzéke megoldja ezt a problémát.  A konfigurálást követően a szolgáltatás-regisztrációs kiszolgáló a szolgáltatás regisztrálását és felderítését fogja szabályozni az alkalmazáshoz tartozó szolgáltatásokhoz. A szolgáltatás beállításjegyzék-kiszolgálója karbantartja az üzembe helyezett szolgáltatások beállításjegyzékét, lehetővé teszi az ügyféloldali terheléselosztást, és leválasztja a szolgáltatókat az ügyfelektől a DNS-re való támaszkodás nélkül.

## <a name="register-your-application-using-spring-cloud-service-registry"></a>Alkalmazás regisztrálása a Spring Cloud Service Registry használatával

Ahhoz, hogy az alkalmazás kezelni tudja a szolgáltatás regisztrációját és felderítését a Spring Cloud Service beállításjegyzékével, számos függőségnek szerepelnie kell az alkalmazás *pom.xml* fájljában.
Függőségek belefoglalása a *Spring-Cloud-Starter-Netflix-Eureka-Client* és *Spring-Cloud-Starter-Azure-Spring-Cloud-client* és a *pom.xml* között

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0</version>
    </dependency>
```

## <a name="update-the-top-level-class"></a>A legfelső szintű osztály frissítése

Végezetül hozzáadunk egy jegyzetet az alkalmazás legfelső szintű osztályához

 ```java
    package foo.bar;

    import org.springframework.boot.SpringApplication;
    import org.springframework.cloud.client.SpringCloudApplication;

    @SpringCloudApplication
    public class DemoApplication {
        public static void main(String... args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
 ```

A Spring Cloud Service beállításjegyzék-kiszolgáló végpontja környezeti változóként lesz befecskendezve az alkalmazásban.  A-szolgáltatások mostantól képesek regisztrálni magukat a szolgáltatás beállításjegyzék-kiszolgálójával, és felfedezhetik a többi függő szolgáltatást is.

Vegye figyelembe, hogy a módosítások a kiszolgálóról az összes-szolgáltatásra történő propagálására is eltarthat néhány percig.
