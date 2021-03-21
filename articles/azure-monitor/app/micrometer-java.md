---
title: A Mikrométer használata az Azure Application Insights Java SDK-val
description: Részletes útmutató a Mikrométerek használatáról Application Insights Spring boot-és nem tavaszi rendszerindító alkalmazásaival.
ms.topic: conceptual
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.date: 11/01/2018
ms.openlocfilehash: 8dba7280f6abd6026fabdde500dc76b73129d557
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100589751"
---
# <a name="how-to-use-micrometer-with-azure-application-insights-java-sdk-not-recommended"></a>A Mikrométer használata az Azure Application Insights Java SDK-val (nem ajánlott)

> [!IMPORTANT]
> A dokumentumban leírt módszer már nem ajánlott.
> 
> A Java-alkalmazások figyelésének ajánlott módszere az automatikus kiépítés használata a kód módosítása nélkül. A Mikrométer telemetria automatikusan összegyűjtve a Application Insights Java 3,0-ügynökkel – kövesse a [Application Insights java 3,0-ügynökhöz](./java-in-process-agent.md)tartozó útmutatást.

> [!NOTE]
> A Application Insights Java SDK nem támogatja a Spring webflux használatát – a [Application Insights Java 3,0-ügynököt](./java-in-process-agent.md) használja helyette. 
>
> A webflux és a Mikrométer is támogatott [Application Insights Java 3,0-ügynökben](./java-on-premises.md) , amely nem igényel rendszerállapotot. 

A Mikrométer alkalmazás-figyelési mérőszámai a JVM-alapú alkalmazás kódjára vonatkoznak, és lehetővé teszi az adatok exportálását kedvenc megfigyelési rendszerbe. Ez a cikk bemutatja, hogyan használható a Mikrométer a Application Insights a Spring boot és a nem rugós rendszerindítási alkalmazásokhoz.

## <a name="using-spring-boot-15x"></a>Spring boot 1.5 x használata
Adja hozzá a következő függőségeket a pom.xml vagy Build. gradle fájlhoz: 
* [Application Insights Spring-boot-Starter](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter) 2.5.0 vagy újabb verzió
* Mikrométer Azure Registry 1.1.0 vagy újabb
* A [mikrométer rugó örökölt](https://micrometer.io/docs/ref/spring/1.5) 1.1.0-es vagy újabb (ez a backports az automatikus konfigurációs kód a Spring Framework-ben).
* [ApplicationInsights erőforrás](./create-new-resource.md)

Lépések

1. Frissítse a Spring boot-alkalmazás pom.xml-fájlját, és adja hozzá az alábbi függőségeket:

    ```XML
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-spring-boot-starter</artifactId>
        <version>2.5.0</version>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-spring-legacy</artifactId>
        <version>1.1.0</version>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-registry-azure-monitor</artifactId>
        <version>1.1.0</version>
    </dependency>

    ```
2. Frissítse az Application. properties vagy a YML fájlt a Application Insights rendszerállapot-kulcs használatával a következő tulajdonsággal:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
1. Az alkalmazás létrehozása és futtatása
2. A fenti lépésekkel a Azure Monitorba automatikusan összegyűjtött, előre összevont metrikákkal kezdheti meg a szolgáltatást. A Application Insights Spring boot Starter finomhangolásával kapcsolatos részletekért tekintse meg a [githubon található Readme](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/spring/azure-spring-boot-starter/README.md)című témakört.

## <a name="using-spring-2x"></a>A Spring 2. x használata

Adja hozzá a következő függőségeket a pom.xml vagy Build. gradle fájlhoz:

* Application Insights Spring-boot-Starter 2.1.2 vagy újabb verzió
* Azure-Spring-boot-metrikák-kezdő 2.0.7 vagy újabb
* [Erőforrás Application Insights](./create-new-resource.md)

Lépések:

1. Frissítse a Spring boot-alkalmazás pom.xml-fájlját, és adja hozzá a következő függőséget:

    ```XML
    <dependency> 
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-spring-boot-metrics-starter</artifactId>
          <version>2.0.7</version>
    </dependency>
    ```
1. Frissítse az Application. properties vagy a YML fájlt a Application Insights rendszerállapot-kulcs használatával a következő tulajdonsággal:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
3. Az alkalmazás létrehozása és futtatása
4. A fentiekben a Azure Monitorba automatikusan összegyűjtött, előre összevont metrikákkal kell futnia. A Application Insights Spring boot Starter finomhangolásával kapcsolatos részletekért tekintse meg a [githubon található Readme](https://github.com/Microsoft/azure-spring-boot/releases/latest)című témakört.

Alapértelmezett mérőszámok:

*    A rendszer automatikusan konfigurálta a Tomcat, a JVM, a Logback metrikák, a Log4J metrikák, a üzemidő Metrikái, a processzor Metrikái és a FileDescriptorMetrics metrikáit.
*    Ha például a Netflix Hystrix megtalálható az osztály elérési úton, akkor a mérőszámokat is megkapjuk. 
*    A következő mérőszámok elérhetők a megfelelő bab hozzáadásával. 
        - CacheMetrics (CaffeineCache, EhCache2, GuavaCache, HazelcastCache, JCache)
        - DataBaseTableMetrics 
        - HibernateMetrics 
        - JettyMetrics 
        - OkHttp3 metrikák 
        - Kafka-metrikák 



Az automatikus metrikák gyűjtésének kikapcsolása: 

- JVM metrikák: 
    - felügyeleti. mérőszámok. kötések. JVM. enabled = false 
- Logback metrikák: 
    - felügyeleti. mérőszámok. kötések. logback. enabled = false
- Üzemidő mérőszámai: 
    - felügyeleti. mérőszámok. kötések. üzemidő. enabled = false 
- Processzor Metrikái:
    -  felügyeleti. mérőszámok. kötések. processzor. enabled = false 
- FileDescriptorMetrics:
    - felügyeleti. mérőszámok. kötések. files. enabled = false 
- Hystrix metrikák, ha a osztályútvonal lévő könyvtár: 
    - felügyeleti. mérőszámok. kötések. Hystrix. enabled = false 
- AspectJ metrikák, ha a osztályútvonal lévő könyvtár: 
    - Spring. AOP. enabled = false 

> [!NOTE]
> Adja meg a fenti tulajdonságokat a Spring boot-alkalmazás Application. properties vagy Application. YML fájljában.

## <a name="use-micrometer-with-non-spring-boot-web-applications"></a>A Mikrométer használata nem rugós rendszerindító webalkalmazásokkal

Adja hozzá a következő függőségeket a pom.xml vagy Build. gradle fájlhoz:

* Application Insights web Auto 2.5.0 vagy újabb verzió
* Mikrométer Azure Registry 1.1.0 vagy újabb
* [Erőforrás Application Insights](./create-new-resource.md)

Lépések:

1. Adja hozzá a következő függőségeket a pom.xml vagy Build. gradle fájlhoz:

    ```XML
        <dependency>
            <groupId>io.micrometer</groupId>
            <artifactId>micrometer-registry-azure-monitor</artifactId>
            <version>1.1.0</version>
        </dependency>

        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>applicationinsights-web-auto</artifactId>
            <version>2.5.0</version>
        </dependency>
     ```

2. `ApplicationInsights.xml`Fájl elhelyezése a Resources mappában:

    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">

       <!-- The key from the portal: -->
       <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>

       <!-- HTTP request component (not required for bare API) -->
       <TelemetryModules>
          <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
       </TelemetryModules>

       <!-- Events correlation (not required for bare API) -->
       <!-- These initializers add context data to each event -->
       <TelemetryInitializers>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
       </TelemetryInitializers>

    </ApplicationInsights>
    ```

3. Példa servlet-osztályra (időzítő metrikát bocsát ki):

    ```Java
        @WebServlet("/hello")
        public class TimedDemo extends HttpServlet {

          private static final long serialVersionUID = -4751096228274971485L;

          @Override
          @Timed(value = "hello.world")
          protected void doGet(HttpServletRequest request, HttpServletResponse response)
              throws ServletException, IOException {

            response.getWriter().println("Hello World!");
            MeterRegistry registry = (MeterRegistry) getServletContext().getAttribute("AzureMonitorMeterRegistry");

        //create new Timer metric
            Timer sampleTimer = registry.timer("timer");
            Stream<Integer> infiniteStream = Stream.iterate(0, i -> i+1);
            infiniteStream.limit(10).forEach(integer -> {
              try {
                Thread.sleep(1000);
                sampleTimer.record(integer, TimeUnit.MILLISECONDS);
              } catch (Exception e) {}
               });
          }
          @Override
          public void init() throws ServletException {
            System.out.println("Servlet " + this.getServletName() + " has started");
          }
          @Override
          public void destroy() {
            System.out.println("Servlet " + this.getServletName() + " has stopped");
          }

        }

    ```

4. Minta konfigurációs osztály:

    ```Java
         @WebListener
         public class MeterRegistryConfiguration implements ServletContextListener {

           @Override
           public void contextInitialized(ServletContextEvent servletContextEvent) {

         // Create AzureMonitorMeterRegistry
           private final AzureMonitorConfig config = new AzureMonitorConfig() {
             @Override
             public String get(String key) {
                 return null;
             }
            @Override
               public Duration step() {
                 return Duration.ofSeconds(60);}

             @Override
             public boolean enabled() {
                 return false;
             }
         };

      MeterRegistry azureMeterRegistry = AzureMonitorMeterRegistry.builder(config);

             //set the config to be used elsewhere
             servletContextEvent.getServletContext().setAttribute("AzureMonitorMeterRegistry", azureMeterRegistry);

           }

           @Override
           public void contextDestroyed(ServletContextEvent servletContextEvent) {

           }
         }
    ```

A metrikákkal kapcsolatos további tudnivalókért tekintse meg a [mikrométer dokumentációját](https://micrometer.io/docs/).

A különböző típusú metrikák létrehozásával kapcsolatos további mintakód[a hivatalos mikrométer GitHub](https://github.com/micrometer-metrics/micrometer/tree/master/samples/micrometer-samples-core/src/main/java/io/micrometer/core/samples)-tárházában található.

## <a name="how-to-bind-additional-metrics-collection"></a>További mérőszámok gyűjtésének kötése

### <a name="springbootspring"></a>SpringBoot/Spring

Hozzon létre egy megfelelő metrikai kategóriába tartozó Bean-osztályt. Tegyük fel például, hogy a guava gyorsítótár Metrikái vannak:

```Java
    @Bean
    GuavaCacheMetrics guavaCacheMetrics() {
        Return new GuavaCacheMetrics();
    }
```
Több mérőszám is van, amelyek alapértelmezés szerint nem engedélyezettek, de a fenti módon kötve lehet. A teljes listát [a hivatalos mikrométer GitHub](https://github.com/micrometer-metrics/micrometer/tree/master/micrometer-core/src/main/java/io/micrometer/core/instrument/binder )-tárházban találja.

### <a name="non-spring-apps"></a>Nem rugós alkalmazások
Adja hozzá a következő kötési kódot a konfigurációs fájlhoz:
```Java 
    New GuavaCacheMetrics().bind(registry);
```

## <a name="next-steps"></a>Következő lépések

* A mikrométersel kapcsolatos további tudnivalókért tekintse meg a hivatalos [mikrométer dokumentációját](https://micrometer.io/docs).
* A Spring on Azure-ról az Azure-beli hivatalos [tavaszi dokumentációban](/java/azure/spring-framework/)olvashat bővebben.
