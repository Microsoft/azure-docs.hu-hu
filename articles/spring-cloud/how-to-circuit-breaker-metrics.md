---
title: A Spring Cloud Resilience4J áramkör-megszakító metrika gyűjtése a Micrometerrel
description: Resilience4J megszakító metrikák gyűjtése Spring Cloud Micrometerrel a Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/15/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: fedebd9182c168b9b7c455d5f6726e66720e0a8b
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479159"
---
# <a name="collect-spring-cloud-resilience4j-circuit-breaker-metrics-with-micrometer-preview"></a>A Spring Cloud Resilience4J áramkör-megszakító metrika gyűjtése a Mikrométerrel (előzetes verzió)

Ez a dokumentum bemutatja, hogyan gyűjthet Spring Cloud Resilience4j megszakító metrikákat Application Insights Java folyamaton belül ügynökkel. Ezzel a funkcióval a Micrometerrel monitorhatja a resilience4j áramkör-megszakító Application Insights metrikákat.

A [spring-cloud-circuit-breaker-demo](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo) segítségével mutatjuk be a működését.

## <a name="prerequisites"></a>Előfeltételek

* Engedélyezze a Java In-Process-ügynököt a [Java In-Process Agent for Application Insights útmutatóban.](./spring-cloud-howto-application-insights.md#enable-java-in-process-agent-for-application-insights) 

* Engedélyezze a dimenziógyűjtést a resilience4j metrikákhoz a [Application Insights útmutatóban.](../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation)

* Telepítse a gitet, a Mavent és a Javát, ha még nincs használatban a fejlesztői számítógép.

## <a name="build-and-deploy-apps"></a>Alkalmazások létrehozása és üzembe helyezése

Az alábbi eljárás alkalmazásokat épít fel és telepít.

1. Klónozza és készítse el a bemutató adattárat.

```bash
git clone https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo.git
cd spring-cloud-circuitbreaker-demo && mvn clean package -DskipTests
```

2. Alkalmazások létrehozása végpontokkal

```azurecli
az spring-cloud app create --name resilience4j --assign-endpoint \
    -s ${asc-service-name} -g ${asc-resource-group}
az spring-cloud app create --name reactive-resilience4j --assign-endpoint \
    -s ${asc-service-name} -g ${asc-resource-group}
```

3. Alkalmazások üzembe helyezése.

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
> * Tartalmazza a Resilience4j kötelező függőségét:
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
> * Az ügyfélkódnak a API-ját kell használnia, amely automatikusan létrehozottként lesz megvalósítva, ha Spring Cloud `CircuitBreakerFactory` `bean` megszakító indítóval. További részletek: [Spring Cloud megszakító.](https://spring.io/projects/spring-cloud-circuitbreaker#overview)
>
> * Az alábbi 2 függőség ütközik a fenti resilient4j csomagokkal.  Győződjön meg arról, hogy az ügyfél nem tartalmazza őket.
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
> Lépjen az átjáróalkalmazások által biztosított URL-címre, és nyissa meg a végpontot a [spring-cloud-circuit-breaker-demo](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo) webhelyről a következőképpen:
>
>   ```console
>   /get
>   /get/delay/{seconds}
>   /get/fluxdelay/{seconds}
>   ```

## <a name="locate-resilence4j-metrics-from-portal"></a>Resilence4j-metrikák keresése a portálon

1. Válassza a **Application Insights** panelt Azure Spring Cloud portálon, majd kattintson a **Application Insights** elemre.

   [![resilience4J 0](media/spring-cloud-resilience4j/resilience4J-0.png)](media/spring-cloud-resilience4j/resilience4J-0.PNG)

2. Válassza **a Metrikák** lehetőséget **Application Insights** lapon.  Válassza **ki az azure.applicationinsights adatokat** a **Metrics namespace (Metrikák névtere) közül.**  A **metrikák resilience4j_circuitbreaker_buffered_calls** az Average (Átlag) **beállítással.**

   [![resilience4J 1](media/spring-cloud-resilience4j/resilience4J-1.png)](media/spring-cloud-resilience4j/resilience4J-1.PNG)

3. Válassza **resilience4j_circuitbreaker_calls** metrikák és az **Átlag lehetőséget.**

   [![resilience4J 2](media/spring-cloud-resilience4j/resilience4J-2.png)](media/spring-cloud-resilience4j/resilience4J-2.PNG)

4. Válassza **resilience4j_circuitbreaker_calls** metrikák és az **Átlag lehetőséget.**  Kattintson **az Add filter (Szűrő hozzáadása)** elemre, majd válassza a name (név) **lehetőséget: createNewAccount**.

   [![resilience4J 3](media/spring-cloud-resilience4j/resilience4J-3.png)](media/spring-cloud-resilience4j/resilience4J-3.PNG)

5. Válassza **resilience4j_circuitbreaker_calls** metrikák és az **Átlag lehetőséget.**  Ezután kattintson a **Felosztás alkalmazása elemre,** és válassza az **kind lehetőséget.**

   [![resilience4J 4](media/spring-cloud-resilience4j/resilience4J-4.png)](media/spring-cloud-resilience4j/resilience4J-4.PNG)

6. Válassza **resilience4j_circuitbreaker_calls**, a **' resilience4j_circuitbreaker_buffered_calls**, és **resilience4j_circuitbreaker_slow_calls** a metrikákat az Average (Átlag) **értékekkel.**

   [![resilience4J 5](media/spring-cloud-resilience4j/resilience4j-5.png)](media/spring-cloud-resilience4j/resilience4j-5.PNG)

## <a name="see-also"></a>Lásd még

* [Application Insights](spring-cloud-howto-application-insights.md)
* [Elosztott nyomkövetés](spring-cloud-howto-distributed-tracing.md)
* [Áramkör-megszakító irányítópult](spring-cloud-tutorial-circuit-breaker.md)
