---
title: Elosztott nyomkövetés használata az Azure Spring Cloud használatával
description: Ismerje meg, hogyan használhatja a Spring Cloud elosztott nyomkövetését az Azure Application Insights
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/06/2019
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 30eb19e418292e74989be81d94ed684c917f6971
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2020
ms.locfileid: "92088635"
---
# <a name="use-distributed-tracing-with-azure-spring-cloud"></a>Elosztott nyomkövetés használata az Azure Spring Cloud használatával

Az Azure Spring Cloud elosztott nyomkövetési eszközeivel könnyedén hibakeresést végezhet, és figyelheti az összetett problémákat. Az Azure Spring Cloud egyesíti a [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) az Azure [Application Insightsával](../azure-monitor/app/app-insights-overview.md). Ez az integráció hatékony elosztott nyomkövetési képességet biztosít a Azure Portal.

::: zone pivot="programming-language-csharp"
Ebből a cikkből megtudhatja, hogyan engedélyezheti a .NET Core Steeltoe-alkalmazások számára az elosztott nyomkövetés használatát.

## <a name="prerequisites"></a>Előfeltételek

Az eljárások követéséhez olyan Steeltoe-alkalmazásra van szüksége, amely már [készen áll az Azure Spring Cloud üzembe helyezésére](spring-cloud-tutorial-prepare-app-deployment.md).

## <a name="dependencies"></a>Függőségek

A következő NuGet-csomagok telepítése

* [Steeltoe. Management. TracingCore](https://www.nuget.org/packages/Steeltoe.Management.TracingCore/)
* [Steeltoe. Management. ExporterCore](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/)

## <a name="update-startupcs"></a>Startup.cs frissítése

1. A `ConfigureServices` metódusban hívja meg a `AddDistributedTracing` és `AddZipkinExporter` metódusokat.

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddDistributedTracing(Configuration);
       services.AddZipkinExporter(Configuration);
   }
   ```

1. A `Configure` metódusban hívja meg a `UseTracingExporter` metódust.

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
        app.UseTracingExporter();
   }
   ```

## <a name="update-configuration"></a>Konfiguráció frissítése

Adja hozzá a következő beállításokat a konfigurációs forráshoz, amelyet az alkalmazás az Azure Spring Cloud-ban való futtatásakor fog használni:

1. A `management.tracing.alwaysSample` paramétert állítsa igaz értékre.

2. Ha látni szeretné az Eureka-kiszolgáló, a konfigurációs kiszolgáló és a felhasználói alkalmazások között eljuttatott nyomkövetési felölelt, állítsa a `management.tracing.egressIgnorePattern` következőre: "/API/v2/spans |/v2/apps/.* /permissions |/Eureka/.*| /oauth/.*".

A *appsettings.json* például a következő tulajdonságokat fogja tartalmazni:
 
```json
"management": {
    "tracing": {
      "alwaysSample": true,
      "egressIgnorePattern": "/api/v2/spans|/v2/apps/.*/permissions|/eureka/.*|/oauth/.*"
    }
  }
```

A .NET Core Steeltoe-alkalmazásokban található elosztott nyomkövetéssel kapcsolatos további információkért lásd: [elosztott nyomkövetés](https://steeltoe.io/docs/3/tracing/distributed-tracing) a Steeltoe dokumentációjában.
::: zone-end
::: zone pivot="programming-language-java"
Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Az elosztott nyomkövetés engedélyezése a Azure Portalban.
> * A Spring Cloud Sleuth hozzáadása az alkalmazáshoz.
> * Tekintse meg a szolgáltatói alkalmazások függőségi térképeit.
> * Keresési nyomkövetési adatkeresés különböző szűrőkkel.

## <a name="prerequisites"></a>Előfeltételek

A fenti eljárások követéséhez olyan Azure Spring Cloud Service-szolgáltatásra van szükség, amely már üzembe van és fut. Fejezze be az [első Azure Spring Cloud-alkalmazás üzembe helyezését](spring-cloud-quickstart.md) az Azure Spring Cloud Service kiépítéséhez és futtatásához.

## <a name="add-dependencies"></a>Függőségek hozzáadása

1. Adja hozzá a következő sort az Application. properties fájlhoz:

   ```xml
   spring.zipkin.sender.type = web
   ```

   A módosítás után a Zipkin küldő küldhet a weben.

1. Ugorja át ezt a lépést, ha követte az [Azure Spring Cloud-alkalmazás előkészítését ismertető útmutatót](spring-cloud-tutorial-prepare-app-deployment.md). Ellenkező esetben lépjen a helyi fejlesztési környezetbe, és szerkessze pom.xml-fájlját, hogy tartalmazza a következő Spring Cloud Sleuth függőséget:

    ```xml
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-sleuth</artifactId>
                <version>${spring-cloud-sleuth.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-sleuth</artifactId>
        </dependency>
    </dependencies>
    ```

1. Hozza létre és telepítse újra az Azure Spring Cloud Service-t, hogy tükrözze ezeket a módosításokat.

## <a name="modify-the-sample-rate"></a>A mintavételi sebesség módosítása

A mintavételi sebesség módosításával módosíthatja a telemetria gyűjtésének gyakoriságát. Ha például a felet gyakran szeretné felvenni, nyissa meg az Application. properties fájlt, és módosítsa a következő sort:

```xml
spring.sleuth.sampler.probability=0.5
```

Ha már létrehozott és telepített egy alkalmazást, módosíthatja a mintavételezési arányt. Ezt úgy teheti meg, ha hozzáadja az előző sort környezeti változóként az Azure CLI-ben vagy a Azure Portal.
::: zone-end

## <a name="enable-application-insights"></a>Az Application Insights engedélyezése

1. Lépjen a Azure Portal Azure Spring Cloud Service oldalára.
1. A **figyelés** lapon válassza az **elosztott nyomkövetés**lehetőséget.
1. Válassza a **beállítás szerkesztése** lehetőséget egy új beállítás szerkesztéséhez vagy hozzáadásához.
1. Hozzon létre egy új Application Insights lekérdezést, vagy válasszon ki egy meglévőt.
1. Válassza ki a figyelni kívánt naplózási kategóriát, és adja meg a megőrzési időt napokban.
1. Az új nyomkövetés alkalmazásához kattintson az **alkalmaz** gombra.

## <a name="view-the-application-map"></a>Az alkalmazás-hozzárendelés megtekintése

Térjen vissza az **elosztott nyomkövetés** lapra, és válassza az **alkalmazás-hozzárendelés megtekintése**lehetőséget. Tekintse át az alkalmazás és a figyelési beállítások vizuális megjelenítését. Az alkalmazás-hozzárendelés használatának megismeréséhez tekintse meg az [Application Map: elosztott alkalmazások osztályozása](../azure-monitor/app/app-map.md)című témakört.

## <a name="use-search"></a>Keresés használata

Más konkrét telemetria-elemek lekérdezéséhez használja a Search függvényt. Az **elosztott nyomkövetés** lapon válassza a **Keresés**lehetőséget. További információ a keresési funkció használatáról: [Keresés használata Application Insightsban](../azure-monitor/app/diagnostic-search.md).

## <a name="use-application-insights"></a>Application Insights használata

A Application Insights az Application Map és a Search függvény mellett biztosít figyelési képességeket. Keresse meg az alkalmazás neve Azure Portal, majd nyisson meg egy Application Insights lapot a figyelési információk megkereséséhez. Az eszközök használatáról további útmutatásért tekintse meg Azure Monitor a [naplók lekérdezéseit](/azure/data-explorer/kusto/query/).

## <a name="disable-application-insights"></a>Application Insights letiltása

1. Lépjen a Azure Portal Azure Spring Cloud Service oldalára.
1. A **figyelés**lapon válassza az **elosztott nyomkövetés**lehetőséget.
1. Application Insights letiltásához válassza a **Letiltás** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan engedélyezheti és értelmezheti az elosztott nyomkövetést az Azure Spring Cloud-ban. Az alkalmazásokhoz való kötési szolgáltatásokkal kapcsolatos további tudnivalókért lásd: [Azure Cosmos db adatbázis kötése egy Azure Spring Cloud-alkalmazáshoz](spring-cloud-tutorial-bind-cosmos.md).