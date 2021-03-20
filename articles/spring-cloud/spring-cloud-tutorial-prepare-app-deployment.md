---
title: Alkalmazás előkészítése üzembe helyezéshez az Azure Spring Cloud-ban
description: Megtudhatja, hogyan készítheti elő az alkalmazást az Azure Spring Cloud üzembe helyezéséhez.
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 4e9c84efe7b96cf61a69c54e3f5ecbc469ac7d8d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98874142"
---
# <a name="prepare-an-application-for-deployment-in-azure-spring-cloud"></a>Alkalmazás előkészítése az Azure Spring Cloud üzembe helyezéséhez

::: zone pivot="programming-language-csharp"
Az Azure Spring Cloud robusztus szolgáltatásokat biztosít a Steeltoe-alkalmazások üzemeltetéséhez, figyeléséhez, méretezéséhez és frissítéséhez. Ez a cikk bemutatja, hogyan készíthet egy meglévő Steeltoe-alkalmazást az Azure Spring Cloud üzembe helyezéséhez. 

Ez a cikk ismerteti azokat a függőségeket, konfigurációkat és kódokat, amelyek szükségesek a .NET Core Steeltoe alkalmazás Azure Spring Cloud-beli futtatásához. További információ az alkalmazások Azure Spring Cloud-beli üzembe helyezéséről: [az első Azure Spring Cloud-alkalmazás üzembe helyezése](spring-cloud-quickstart.md).

>[!Note]
> Az Azure Spring Cloud Steeltoe-támogatása jelenleg nyilvános előzetes verzióként érhető el. A nyilvános előzetes ajánlatok lehetővé teszik, hogy az ügyfelek a hivatalos kiadásuk előtt új funkciókkal kísérletezzenek.  A nyilvános előzetes verzió funkcióit és szolgáltatásait nem éles használatra szánták.  További információ az előzetes verziók támogatásáról: [Gyakori kérdések](https://azure.microsoft.com/support/faq/) vagy a [support Request](../azure-portal/supportability/how-to-create-azure-support-request.md)fájl.

##  <a name="supported-versions"></a>Támogatott verziók

Az Azure Spring Cloud a következőket támogatja:

* .NET Core 3.1
* Steeltoe 2,4 és 3,0

## <a name="dependencies"></a>Függőségek

A Steeltoe 2,4 esetében adja hozzá a legújabb [Microsoft. Azure. SpringCloud. Client 1. x. x](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/) csomagot a Project fájlhoz:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="1.0.0-preview.1" />
  <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Extensions.Configuration.ConfigServerCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Management.TracingCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Management.ExporterCore" Version="2.4.4" />
</ItemGroup>
```

A Steeltoe 3,0-es verziójában adja hozzá a legújabb [Microsoft. Azure. SpringCloud. Client 2. x. x](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/) csomagot a Project fájlhoz:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="2.0.0-preview.1" />
  <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="3.0.0" />
  <PackageReference Include="Steeltoe.Extensions.Configuration.ConfigServerCore" Version="3.0.0" />
  <PackageReference Include="Steeltoe.Management.TracingCore" Version="3.0.0" />
</ItemGroup>
```

## <a name="update-programcs"></a>Program frissítése. cs

A `Program.Main` metódusban hívja meg a `UseAzureSpringCloudService` metódust.

A Steeltoe 2.4.4 esetében hívja meg a következőt `UseAzureSpringCloudService` `ConfigureWebHostDefaults` : és után, `AddConfigServer` Ha a neve:

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

A Steeltoe 3.0.0 esetében a `UseAzureSpringCloudService` `ConfigureWebHostDefaults` Steeltoe-konfigurációs kód előtt és előtt hívja meg a következőt:

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

## <a name="enable-eureka-server-service-discovery"></a>Az Eureka Server Service Discovery engedélyezése

Azon a konfigurációs forráson, amelyet az alkalmazás az Azure Spring Cloud-ban való futtatásakor fog használni, állítsa a `spring.application.name` nevet ugyanarra a névre, mint az Azure Spring Cloud alkalmazásra, amelyhez a projekt telepítve lesz.

Ha például egy nevű .NET-projektet helyez üzembe `EurekaDataProvider` aappSettings.jsfájl nevű Azure Spring Cloud-alkalmazáshoz, a `planet-weather-provider` következő JSON-t kell tartalmaznia: 

```json
"spring": {
  "application": {
    "name": "planet-weather-provider"
  }
}
```

## <a name="use-service-discovery"></a>A szolgáltatás felderítésének használata

Ha az Eureka Server Service Discovery használatával szeretne meghívni egy szolgáltatást, a HTTP-kérelmeket a `http://<app_name>` `app_name` cél alkalmazás értékeként kell meghívnia `spring.application.name` . A következő kód például meghívja a `planet-weather-provider` szolgáltatást:

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
Ez a témakör bemutatja, hogyan készíthet meglévő Java Spring-alkalmazást az Azure Spring Cloud üzembe helyezéséhez. Ha megfelelően van konfigurálva, az Azure Spring Cloud robusztus szolgáltatásokat biztosít a Java Spring Cloud-alkalmazás monitorozásához, méretezéséhez és frissítéséhez.

A példa futtatása előtt próbálja ki az [alapszintű](spring-cloud-quickstart.md)rövid útmutatót.

Más példák azt is ismertetik, hogyan helyezhet üzembe egy alkalmazást az Azure Spring-felhőben, ha a POM-fájl konfigurálva van. 
* [Az első alkalmazás elindítása](spring-cloud-quickstart.md)
* [Mikroszolgáltatások létrehozása és futtatása](spring-cloud-quickstart-sample-app-introduction.md)

Ez a cikk a szükséges függőségeket és azok hozzáadását ismerteti a POM-fájlhoz.

## <a name="java-runtime-version"></a>Java futtatókörnyezet verziója

Csak a Spring/Java-alkalmazások futhatnak az Azure Spring Cloud-ban.

Az Azure Spring Cloud a Java 8 és a Java 11 használatát is támogatja. Az üzemeltetési környezet tartalmazza az Azure-hoz készült Azul Zulu OpenJDK legújabb verzióját. További információ az Azure-hoz készült Azul Zulu OpenJDK: [a JDK telepítése](/azure/developer/java/fundamentals/java-jdk-install).

## <a name="spring-boot-and-spring-cloud-versions"></a>A Spring boot és a Spring Cloud verziói

Egy meglévő Spring boot-alkalmazás előkészítéséhez az Azure Spring Cloud-ba történő üzembe helyezéshez az alkalmazás POM-fájljának Spring boot és Spring Cloud függőségei szerepelnek, ahogy az a következő részben látható.

Az Azure Spring Cloud csak Spring boot-alkalmazásokat támogat, vagy a Spring boot 2,1-es vagy 2,2-os verzióját. A következő táblázat a támogatott Spring boot és Spring felhő kombinációkat sorolja fel:

Spring boot-verzió | Tavaszi felhő verziója
---|---
2,2 | Hoxton. SR8
2.3 | Hoxton. SR8
2.4.1 + | 2020.0.0

> [!NOTE]
> A Spring boot 2.4.0 az alkalmazások és az Eureka közötti TLS-hitelesítéssel kapcsolatos hibát észlelt, használja a 2.4.1 vagy újabb rendszert. Ha ragaszkodik a 2.4.0 használatához, tekintse meg a megkerülő megoldással kapcsolatos [gyakori kérdéseket](./spring-cloud-faq.md?pivots=programming-language-java#development) .

### <a name="dependencies-for-spring-boot-version-2223"></a>A Spring boot 2.2/2.3-as verziójának függőségei

A Spring boot 2,2-es verziójában adja hozzá a következő függőségeket az Application POM-fájlhoz.

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

### <a name="dependencies-for-spring-boot-version-24"></a>A Spring boot 2,4-es verziójának függőségei

A Spring boot 2,2-es verziójában adja hozzá a következő függőségeket az Application POM-fájlhoz.

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
> Ne adja meg `server.port` a konfigurációt. Az Azure Spring Cloud ezt a beállítást egy rögzített Overide fogja beállítani. Vegye figyelembe ezt a beállítást is, és ne adja meg a kiszolgáló portját a kódban.

## <a name="other-recommended-dependencies-to-enable-azure-spring-cloud-features"></a>Egyéb ajánlott függőségek az Azure Spring Cloud funkcióinak engedélyezéséhez

Annak érdekében, hogy az Azure Spring Cloud beépített funkciói a szolgáltatás-beállításjegyzékből az elosztott nyomkövetésre legyenek kialakítva, a következő függőségeket is meg kell adnia az alkalmazásban. Ezen függőségek némelyikét elhúzhatja, ha nincs szüksége a megfelelő szolgáltatásokra az adott alkalmazásokhoz.

### <a name="service-registry"></a>Szolgáltatás beállításjegyzéke

A felügyelt Azure szolgáltatás beállításjegyzék-szolgáltatásának használatához adja `spring-cloud-starter-netflix-eureka-client` meg a függőséget a pom.xml fájlban az itt látható módon:

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

A szolgáltatás beállításjegyzék-kiszolgálójának végpontját a rendszer automatikusan környezeti változóként adja meg az alkalmazásban. Az alkalmazások regisztrálhatják magukat a szolgáltatás beállításjegyzék-kiszolgálójával, és felfedezhetik a többi függő szolgáltatást is.


#### <a name="enablediscoveryclient-annotation"></a>EnableDiscoveryClient jegyzet

Adja hozzá a következő megjegyzést az alkalmazás forráskódja számára.
```java
@EnableDiscoveryClient
```
Például tekintse meg a korábbi példákból származó piggymetrics alkalmazást:
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

Az elosztott konfiguráció engedélyezéséhez vegye fel a következő `spring-cloud-config-client` függőséget a pom.xml fájl függőségek szakaszába:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Ne adja meg a `spring.cloud.config.enabled=false` bootstrap konfigurációját. Ellenkező esetben az alkalmazás nem működik a konfigurációs kiszolgálóval.

### <a name="metrics"></a>Mérőszámok

Vegye fel a függőséget a `spring-boot-starter-actuator` pom.xml fájl függőségek szakaszába, ahogy az itt látható:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 A metrikák rendszeres időközönként a JMX-végpontokról vannak leképezve. A metrikákat a Azure Portal használatával jelenítheti meg.

 > [!WARNING]
 > Adja meg a `spring.jmx.enabled=true` konfigurációs tulajdonságot. Ellenkező esetben a metrikák nem megjeleníthetők Azure Portalban.

### <a name="distributed-tracing"></a>Elosztott nyomkövetés

Emellett engedélyeznie kell egy Azure Application Insights-példány működését az Azure Spring Cloud Service-példányával. Az Azure Spring Cloudtel való Application Insights használatáról az [elosztott nyomkövetés dokumentációjában](spring-cloud-tutorial-distributed-tracing.md)talál további információt.

#### <a name="spring-boot-2223"></a>Spring boot 2.2/2.3
Adja meg a következő `spring-cloud-starter-sleuth` és `spring-cloud-starter-zipkin` függőségeket a pom.xml fájl függőségek szakaszában:

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

#### <a name="spring-boot-24"></a>Spring boot 2,4
Adja meg a következő függőséget a `spring-cloud-sleuth-zipkin` pom.xml fájl függőségek részében:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-sleuth-zipkin</artifactId>
</dependency>
```

## <a name="see-also"></a>Lásd még
* [Alkalmazások naplófájljainak és metrikáinak elemzése](./diagnostic-services.md)
* [A Config Server beállítása](./spring-cloud-tutorial-config-server.md)
* [Elosztott nyomkövetés használata az Azure Spring Cloud használatával](./spring-cloud-tutorial-distributed-tracing.md)
* [Spring rövid útmutató](https://spring.io/quickstart)
* [Spring boot – dokumentáció](https://spring.io/projects/spring-boot)

## <a name="next-steps"></a>Következő lépések

Ebben a témakörben megtanulta, hogyan konfigurálhatja a Java Spring-alkalmazást az Azure Spring Cloud üzembe helyezéséhez. A konfigurációs kiszolgálópéldány beállításával kapcsolatos további információkért lásd: [konfigurációs kiszolgálói példány beállítása](spring-cloud-tutorial-config-server.md).

További minták érhetők el a GitHubon: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).
::: zone-end