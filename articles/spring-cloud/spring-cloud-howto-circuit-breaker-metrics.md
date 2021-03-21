---
title: Spring Cloud Resilience4J-megszakító metrikáinak gyűjtése
description: A Spring Cloud Resilience4J-megszakító metrikáinak összegyűjtése.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/15/2020
ms.custom: devx-track-java
ms.openlocfilehash: 0227fcebd6d5a136e3306573b7b5aa0d6e39ba1f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102122799"
---
# <a name="collect-spring-cloud-resilience4j-circuit-breaker-metrics-preview"></a>Spring Cloud Resilience4J áramkör-megszakító metrikáinak összegyűjtése (előzetes verzió)

Ebből a dokumentumból megtudhatja, hogyan gyűjthet a Spring Cloud Resilience4j Circuit Breaker mérőszámait Application Insights Java folyamaton belüli ügynökkel.  Ezzel a funkcióval nyomon követheti a resilience4j áramkör-megszakító metrikáit Application Insightsról.

A [Spring-Cloud-Circuit-Breaker-demo](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo) használatával megmutatjuk, hogyan működik.

## <a name="prerequisites"></a>Előfeltételek

* A Java-In-Process ügynök engedélyezése a [java In-Process-ügynökből Application Insights útmutatóhoz](./spring-cloud-howto-application-insights.md#enable-java-in-process-agent-for-application-insights). 

* A [Application Insights útmutatóban](../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation)engedélyezze a resilience4j-metrikák dimenzióinak gyűjtését.

* Ha a fejlesztői számítógép még nem használja, telepítse a git, a Maven és a Java rendszert.

## <a name="build-and-deploy-apps"></a>Alkalmazások létrehozása és üzembe helyezése

Az alábbi eljárás az alkalmazásokat építi és telepíti.

1. A bemutató tárház klónozása és összeállítása.

```bash
git clone https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo.git
cd spring-cloud-circuitbreaker-demo && mvn clean package -DskipTests
```

2. Végpontokkal rendelkező alkalmazások létrehozása

```azurecli
az spring-cloud app create --name resilience4j --assign-endpoint \
    -s ${asc-service-name} -g ${asc-resource-group}
az spring-cloud app create --name reactive-resilience4j --assign-endpoint \
    -s ${asc-service-name} -g ${asc-resource-group}
```

3. Alkalmazások telepítése.

```azurecli
az spring-cloud app deploy -n resilience4j \
    --jar-path ./spring-cloud-circuitbreaker-demo-resilience4j/target/spring-cloud-circuitbreaker-demo-resilience4j-0.0.1.BUILD-SNAPSHOT.jar \
    -s ${service_name} -g ${resource_group}
az spring-cloud app deploy -n reactive-resilience4j \
    --jar-path ./spring-cloud-circuitbreaker-demo-reactive-resilience4j/target/spring-cloud-circuitbreaker-demo-reactive-resilience4j-0.0.1.BUILD-SNAPSHOT.jar \
    -s ${service_name} -g ${resource_group}
```

> [!Note]
>
> * Adja meg a Resilience4j szükséges függőséget:
>
>   ```xml
>   <dependency>
>       <groupId>io.github.resilience4j</groupId>
>       <artifactId>resilience4j-micrometer</artifactId>
>   </dependency>
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-circuitbreaker-resilience4j</artifactId>
>   </dependency>
>   ```
> * Az ügyfél kódjának a () API-ját kell használnia `CircuitBreakerFactory` , amely `bean` automatikusan létrejön, amikor a rugós Felhőbeli áramkör-megszakítót is tartalmazza. Részletekért lásd: [Spring Cloud Circuit Breaker](https://spring.io/projects/spring-cloud-circuitbreaker#overview).
>
> * A következő két függőség ütközik a fenti resilient4j-csomagokkal.  Ügyeljen arra, hogy az ügyfél ne tartalmazza azokat.
>
>   ```xml
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-sleuth</artifactId>
>   </dependency>
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-zipkin</artifactId>
>   </dependency>
>   ```
>
>
> Navigáljon az átjáró-alkalmazások által megadott URL-címhez, és nyissa meg a végpontot a [Spring-Cloud-Circuit-megszakító-demóból](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo) a következőképpen:
>
>   ```console
>   /get
>   /get/delay/{seconds}
>   /get/fluxdelay/{seconds}
>   ```

## <a name="locate-resilence4j-metrics-from-portal"></a>Resilence4j-metrikák megkeresése a portálról

1. Válassza ki a **Application Insights** panelt az Azure Spring Cloud Portalon, és kattintson az **Application Insights** elemre.

   [![resilience4J 0](media/spring-cloud-resilience4j/resilience4J-0.png)](media/spring-cloud-resilience4j/resilience4J-0.PNG)

2. Válassza a **metrikák** lehetőséget a **Application Insights** lapon.  Válassza az **Azure. applicationinsights** elemet a **metrikák névtérből**.  Válassza ki az **átlagos** **resilience4j_circuitbreaker_buffered_calls** metrikákat is.

   [![1. resilience4J](media/spring-cloud-resilience4j/resilience4J-1.png)](media/spring-cloud-resilience4j/resilience4J-1.PNG)

3. Válassza ki **resilience4j_circuitbreaker_calls** mérőszámokat és **átlagot**.

   [![2. resilience4J](media/spring-cloud-resilience4j/resilience4J-2.png)](media/spring-cloud-resilience4j/resilience4J-2.PNG)

4. Válassza ki **resilience4j_circuitbreaker_calls**  mérőszámokat és **átlagot**.  Kattintson a **szűrő hozzáadása** lehetőségre, majd válassza a név **createNewAccount** lehetőséget.

   [![3. resilience4J](media/spring-cloud-resilience4j/resilience4J-3.png)](media/spring-cloud-resilience4j/resilience4J-3.PNG)

5. Válassza ki **resilience4j_circuitbreaker_calls**  mérőszámokat és **átlagot**.  Ezután kattintson a **felosztás alkalmazása** lehetőségre, és válassza a **fajta** lehetőséget.

   [![4. resilience4J](media/spring-cloud-resilience4j/resilience4J-4.png)](media/spring-cloud-resilience4j/resilience4J-4.PNG)

6. Válassza a **resilience4j_circuitbreaker_calls**, "**resilience4j_circuitbreaker_buffered_calls** és **resilience4j_circuitbreaker_slow_calls** mérőszámok **átlagot**.

   [![5. resilience4J](media/spring-cloud-resilience4j/resilience4j-5.png)](media/spring-cloud-resilience4j/resilience4j-5.PNG)

## <a name="see-also"></a>Lásd még

* [Application bepillantást](./spring-cloud-howto-application-insights.md)
* [Elosztott nyomkövetés](spring-cloud-tutorial-distributed-tracing.md)
* [Áramkör-megszakító irányítópultja](spring-cloud-tutorial-circuit-breaker.md)