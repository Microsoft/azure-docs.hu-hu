---
title: Alkalmazás előkészítése az üzembe helyezéshez a Azure Spring Cloud
description: Megtudhatja, hogyan készíthet elő egy alkalmazást az üzembe helyezéshez a Azure Spring Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: cabc4784dfb19f569212f4d0cb93e6838473e559
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714487"
---
# <a name="prepare-an-application-for-deployment-in-azure-spring-cloud"></a>Alkalmazás előkészítése az üzembe helyezéshez a Azure Spring Cloud

::: zone pivot="programming-language-csharp"
Azure Spring Cloud-alkalmazások hatékony szolgáltatásokat kínálnak egy Ön által is elérhető, Illetve egy Áruházbeli alkalmazás gazdagépeként, monitorzhatóként, skálázhatóként és frissítve. Ez a cikk bemutatja, hogyan készíthet elő egy meglévő EgyEde-alkalmazást a Azure Spring Cloud. 

Ez a cikk ismerteti a .NET Core-beli Azure Spring Cloud.NET Core-alkalmazások futtatásához szükséges függőségeket, konfigurációkat és Azure Spring Cloud. További információ az alkalmazások központi telepítéséhez a [Azure Spring Cloud: Deploy your first Azure Spring Cloud application](spring-cloud-quickstart.md)(Az első alkalmazás Azure Spring Cloud telepítése).

>[!Note]
> A Azure Spring Cloud támogatása jelenleg nyilvános előzetes verzióként érhető el. A nyilvános előzetes verziójú ajánlatok lehetővé teszik, hogy az ügyfelek a hivatalos kiadásuk előtt kísérletezzen az új funkciókkal.  A nyilvános előzetes verziójú funkciók és szolgáltatások nem éles környezetben használhatók.  Az előzetes verziókban való támogatással kapcsolatos további információkért tekintse meg [a](../azure-portal/supportability/how-to-create-azure-support-request.md)gyakori kérdéseket, vagy Support request. [](https://azure.microsoft.com/support/faq/)

##  <a name="supported-versions"></a>Támogatott verziók

Azure Spring Cloud támogatja a következőt:

* .NET Core 3.1
* Cél 2.4 és 3.0

## <a name="dependencies"></a>Függőségek

A 2.4-es Verzióhoz adja hozzá a legújabb [Microsoft.Azure.SpringCloud.Client 1.x.x](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/) csomagot a projektfájlhoz:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="1.0.0-preview.1" />
  <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Extensions.Configuration.ConfigServerCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Management.TracingCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Management.ExporterCore" Version="2.4.4" />
</ItemGroup>
```

A 3.0-s Verzióhoz adja hozzá a legújabb [Microsoft.Azure.SpringCloud.Client 2.x.x](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/) csomagot a projektfájlhoz:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="2.0.0-preview.1" />
  <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="3.0.0" />
  <PackageReference Include="Steeltoe.Extensions.Configuration.ConfigServerCore" Version="3.0.0" />
  <PackageReference Include="Steeltoe.Management.TracingCore" Version="3.0.0" />
</ItemGroup>
```

## <a name="update-programcs"></a>A Program.cs frissítése

A `Program.Main` metódusban hívja meg a `UseAzureSpringCloudService` metódust.

A 2.4.4.4-es, majd az után hívja meg a `UseAzureSpringCloudService` `ConfigureWebHostDefaults` `AddConfigServer` következőt:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        })
        .AddConfigServer()
        .UseAzureSpringCloudService();
```

A 3.0.0-s és a 3.0-sSzámú Fttoe-kódhoz hívja meg a(3)-et a `UseAzureSpringCloudService` `ConfigureWebHostDefaults` Bármely Egyedek konfigurációs kódja előtt:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .UseAzureSpringCloudService()
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        })
        .AddConfigServer();
```

## <a name="enable-eureka-server-service-discovery"></a>Eureka Server-szolgáltatásfelderítés engedélyezése

A konfigurációs forrásban, amelyet az alkalmazás a Azure Spring Cloud-ban való futtatásakor fog használni, állítsa ugyanazt a nevet, mint Azure Spring Cloud alkalmazás, amelyre a projektet telepíteni `spring.application.name` fogja.

Ha például egy nevű .NET-projektet helyez üzembe egy Azure Spring Cloud nevűappSettings.jsfájlban, a következő JSON-nak kell `EurekaDataProvider` `planet-weather-provider` lennie: 

```json
"spring": {
  "application": {
    "name": "planet-weather-provider"
  }
}
```

## <a name="use-service-discovery"></a>Szolgáltatásfelderítés használata

Ha az Eureka Server szolgáltatásfelderítéssel meg kell hívnia egy szolgáltatást, http-kéréseket kell a célalkalmazás értékeként `http://<app_name>` `app_name` `spring.application.name` használnia. A következő kód például a szolgáltatást hívja `planet-weather-provider` meg:

```csharp
using (var client = new HttpClient(discoveryHandler, false))
{
    var responses = await Task.WhenAll(
        client.GetAsync("http://planet-weather-provider/weatherforecast/mercury"),
        client.GetAsync("http://planet-weather-provider/weatherforecast/saturn"));
    var weathers = await Task.WhenAll(from res in responses select res.Content.ReadAsStringAsync());
    return new[]
    {
        new KeyValuePair<string, string>("Mercury", weathers[0]),
        new KeyValuePair<string, string>("Saturn", weathers[1]),
    };
}
```
::: zone-end

::: zone pivot="programming-language-java"
Ez a témakör bemutatja, hogyan készíthet elő egy meglévő Java Spring-alkalmazást a Azure Spring Cloud. Ha megfelelően van konfigurálva, Azure Spring Cloud a Java-alkalmazások figyelése, méretezése és frissítése Spring Cloud szolgáltatásokat.

A példa futtatása előtt próbálja ki az [alapszintű rövid útmutatót.](spring-cloud-quickstart.md)

Más példák bemutatják, hogyan helyezhet üzembe egy alkalmazást Azure Spring Cloud a POM-fájl konfigurálásakor. 
* [Az első alkalmazás elindítása](spring-cloud-quickstart.md)
* [Mikroszolgáltatások létrehozása és futtatása](spring-cloud-quickstart-sample-app-introduction.md)

Ez a cikk ismerteti a szükséges függőségeket, és hogy hogyan adjuk hozzá őket a POM-fájlhoz.

## <a name="java-runtime-version"></a>Java-futásidejű verzió

Csak a Spring/Java-alkalmazások futtathatók a Azure Spring Cloud.

Azure Spring Cloud a Java 8-at és a Java 11-et is támogatja. Az üzemeltetési környezet tartalmazza az Azul Zulu OpenJDK for Azure legújabb verzióját. Az Azul Zulu OpenJDK for Azure-ral kapcsolatos további információkért lásd: Install the JDK (A [JDK telepítése).](/azure/developer/java/fundamentals/java-jdk-install)

## <a name="spring-boot-and-spring-cloud-versions"></a>Spring Boot és Spring Cloud verziók

Egy meglévő Spring Boot-alkalmazás Azure Spring Cloud való üzembe helyezéséhez foglalja bele a Spring Boot és Spring Cloud függőségeket az alkalmazás POM-fájljában, ahogy az a következő szakaszokban látható.

Azure Spring Cloud a Spring Boot 2.2, 2.3 és 2.4 verziót támogatja. Az alábbi táblázat a támogatott Spring Boot és Spring Cloud sorolja fel:

Spring Boot verziója | Spring Cloud verziója
---|---
2,2 | Hoxton.SR8
2.3 | Hoxton.SR8
2.4.1+ | 2020.0.0

> [!NOTE]
> Problémát azonosítottunk az Spring Boot És Eureka közötti TLS-hitelesítés 2.4.0-s vagy magasabb kiadásával kapcsolatban. Használja a 2.4.1-es vagy magasabbat. Ha a [](./spring-cloud-faq.md?pivots=programming-language-java#development) 2.4.0-s vagy a 2.4.0-s megoldást használja, tekintse meg a gyakori kérdéseket az áthidaló megoldáshoz.

### <a name="dependencies-for-spring-boot-version-2223"></a>A Spring Boot 2.2/2.3-as verziójának függőségei

A Spring Boot 2.2-es verziója esetében adja hozzá a következő függőségeket az alkalmazás POM-fájlhoz.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.4.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR8</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="dependencies-for-spring-boot-version-24"></a>A 2.4-es Spring Boot függőségei

A Spring Boot 2.2-es verziója esetében adja hozzá a következő függőségeket az alkalmazás POM-fájlhoz.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.4.1.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>2020.0.0</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

> [!WARNING]
> Ne adja meg a `server.port` értéket a konfigurációban. Azure Spring Cloud ezt a beállítást egy rögzített portszámra fogja túltenni. Ezt a beállítást is tartsa tiszteletben, és ne adjon meg kiszolgálóportot a kódban.

## <a name="other-recommended-dependencies-to-enable-azure-spring-cloud-features"></a>Egyéb ajánlott függőségek a Azure Spring Cloud engedélyezéséhez

A szolgáltatásjegyzékből az elosztott nyomkövetésbe Azure Spring Cloud beépített szolgáltatásainak engedélyezéséhez a következő függőségeket is bele kell foglalnia az alkalmazásba. Ha nincs szüksége a megfelelő funkciókra az adott alkalmazásokhoz, eldobhat néhányat ezek közül a függőségek közül.

### <a name="service-registry"></a>Szolgáltatásjegyzék

A felügyelt Azure Service Registry szolgáltatáshoz az alább látható módon foglalja bele a függőséget `spring-cloud-starter-netflix-eureka-client` pom.xml fájlba:

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

A Service Registry-kiszolgáló végpontja automatikusan be lesz injektálva környezeti változóként az alkalmazással. Az alkalmazások regisztrálják magukat a Service Registry-kiszolgálón, és felderíthetik a többi függő mikroszolgáltatást.


#### <a name="enablediscoveryclient-annotation"></a>EnableDiscoveryClient jegyzet

Adja hozzá a következő jegyzetet az alkalmazás forráskódhoz.
```java
@EnableDiscoveryClient
```
Tekintse meg például a piggymetrics alkalmazást a korábbi példákból:
```java
package com.piggymetrics.gateway;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;

@SpringBootApplication
@EnableDiscoveryClient
@EnableZuulProxy

public class GatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(GatewayApplication.class, args);
    }
}
```

### <a name="distributed-configuration"></a>Elosztott konfiguráció

Az Elosztott konfiguráció engedélyezéséhez foglalja bele a következő függőséget a fájl `spring-cloud-config-client` dependencies (függőségek) pom.xml fájlba:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Ne adja meg a `spring.cloud.config.enabled=false` értéket a bootstrap-konfigurációban. Ellenkező esetben az alkalmazás nem dolgozik tovább a Config Server.

### <a name="metrics"></a>Mérőszámok

A függőséget az alább látható módon foglalja bele `spring-boot-starter-actuator` pom.xml fájl dependencies (függőségek) szakaszba:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 A rendszer rendszeres időközönként lekért metrikákat a JMX-végpontról. A metrikákat a következő Azure Portal.

 > [!WARNING]
 > Adja meg `spring.jmx.enabled=true` a értéket a konfigurációs tulajdonságban. Ellenkező esetben a metrikák nem vizualizálhatóak a Azure Portal.

### <a name="distributed-tracing"></a>Elosztott nyomkövetés

Emellett engedélyeznie kell egy Azure Application Insights-példányt, hogy működjön a Azure Spring Cloud szolgáltatáspéldányával. Az elosztott nyomkövetéssel kapcsolatos Application Insights Azure Spring Cloud információkért lásd: [.](spring-cloud-tutorial-distributed-tracing.md)

#### <a name="spring-boot-2223"></a>Spring Boot 2.2/2.3
A következő és függőségeket foglalja bele a fájl `spring-cloud-starter-sleuth` `spring-cloud-starter-zipkin` dependencies (függőségek) pom.xml fájlba:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

#### <a name="spring-boot-24"></a>Spring Boot 2.4
A következő függőséget foglalja bele a fájl `spring-cloud-sleuth-zipkin` dependencies (függőségek) pom.xml fájlba:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-sleuth-zipkin</artifactId>
</dependency>
```

## <a name="see-also"></a>Lásd még
* [Alkalmazásnaplók és -metrikák elemzése](./diagnostic-services.md)
* [A Config Server beállítása](spring-cloud-tutorial-config-server.md)
* [Elosztott nyomkövetés használata a Azure Spring Cloud](spring-cloud-tutorial-distributed-tracing.md)
* [Spring – rövid útmutató](https://spring.io/quickstart)
* [Spring Boot dokumentációja](https://spring.io/projects/spring-boot)

## <a name="next-steps"></a>Következő lépések

Ebben a témakörben megtanulta, hogyan konfigurálhatja a Java Spring-alkalmazást a Azure Spring Cloud. A példánypéldányok beállításának Config Server lásd: Új példány [Config Server beállítása.](spring-cloud-tutorial-config-server.md)

További minták érhetők el a GitHubon: [Azure Spring Cloud.](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples)
::: zone-end