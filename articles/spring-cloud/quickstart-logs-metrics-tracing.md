---
title: Rövid útmutató – Azure Spring Cloud monitorozása naplók, metrikák és nyomkövetés segítségével
description: Naplóstreamelés, naplóelemzés, metrikák és nyomkövetés használata a Piggymetrics mintaalkalmazások monitorozásához a Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/04/2020
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 907bf06323d13b2d26dec5003e4739f2ae9faf74
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378516"
---
# <a name="quickstart-monitoring-azure-spring-cloud-apps-with-logs-metrics-and-tracing"></a>Rövid útmutató: Azure Spring Cloud monitorozása naplók, metrikák és nyomkövetés segítségével

::: zone pivot="programming-language-csharp"
Az alkalmazás beépített monitorozási Azure Spring Cloud összetett problémák hibakeresését és monitorozását teszi lehetővé. Azure Spring Cloud Az Azure-beli elosztott nyomkövetést [integrálja](https://steeltoe.io/docs/3/tracing/distributed-tracing) az Azure [Application Insights.](../azure-monitor/app/app-insights-overview.md) Ez az integráció hatékony naplókat, metrikákat és elosztott nyomkövetési képességeket biztosít a Azure Portal.

Az alábbi eljárások ismertetik, hogyan használható a Log Streaming, a Log Analytics, a Metrikák és az Elosztott nyomkövetés az előző rövid útmutatókban üzembe helyezett mintaalkalmazással.

## <a name="prerequisites"></a>Előfeltételek

* A sorozat előző rövid útmutatói:

  * [A Azure Spring Cloud kiépítése.](spring-cloud-quickstart-provision-service-instance.md)
  * [A konfigurációs Azure Spring Cloud beállítása.](spring-cloud-quickstart-setup-config-server.md)
  * [Alkalmazások fejlesztése és üzembe helyezése.](spring-cloud-quickstart-deploy-apps.md)

## <a name="logs"></a>Naplók

A naplókat kétféleképpen lehet Azure Spring Cloud **naplók** megtekintése: a valós idejű naplók alkalmazáspéldányonkénti streamelése vagy a **Log Analytics** a speciális lekérdezési képességgel rendelkező összesített naplókhoz.

### <a name="log-streaming"></a>Naplóstreamelés

Az Azure CLI-beli naplóstreamelést a következő paranccsal használhatja.

```azurecli
az spring-cloud app logs -n solar-system-weather -f
```

Az alábbi példához hasonló kimenetet fog látni:

```output
=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Executing action method Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather) - Validation state: Valid
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController[0]

=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Retrieved weather data from 4 planets
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]

=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Executing ObjectResult, writing value of type 'System.Collections.Generic.KeyValuePair`2[[System.String, System.Private.CoreLib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=7cec85d7bea7798e],[System.String, System.Private.CoreLib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=7cec85d7bea7798e]][]'.
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
```

> [!TIP]
> A `az spring-cloud app logs -h` használatával további paramétereket és naplóstreamelési funkciókat tár fel.

### <a name="log-analytics"></a>Log Analytics

1. A Azure Portal a szolgáltatás **| Áttekintés** lap, és **válassza a Monitorozás** szakaszban a **Naplók** lehetőséget. Válassza **a Futtatás** az egyik mintalekérdezéseken Azure Spring Cloud.

   [![Logs Analytics-bejegyzés ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png)](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png#lightbox)
    
1. Szerkessze a lekérdezést, hogy eltávolítsa a Where záradékokat, amelyek a figyelmeztetési és hibanaplókra korlátozzák a megjelenítést.

1. Ezután válassza a `Run` lehetőséget, és látni fogja a naplókat. A [lekérdezések írásával kapcsolatos](../azure-monitor/logs/get-started-queries.md) további útmutatásért tekintse meg az Azure Log Analytics-dokumentumokban található útmutatást.

   [![Logs Analytics-lekérdezés – Logotoe ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query-steeltoe.png#lightbox)

1. A Log Analyticsben használt lekérdezési nyelvvel kapcsolatos további információkért lásd a naplólekérdezések [Azure Monitor.](/azure/data-explorer/kusto/query/) Az összes Log Analytics-napló központi ügyfélről való lekérdezéséhez tekintse meg a [Azure Data Explorer.](https://docs.microsoft.com/azure/data-explorer/query-monitor-data)

## <a name="metrics"></a>Mérőszámok

1. A Azure Portal a szolgáltatás **| Áttekintés** lap, és válassza **a Metrikák** lehetőséget a **Monitorozás** szakaszban. Adja hozzá az első metrikát úgy, hogy kiválasztja az egyik .NET-metrikát a Teljesítmény **(.NET)** vagy a Kérelem **(.NET)** alatt a Metrika legördülő menüben, az Összesítés lehetőség kiválasztásával pedig a metrika  idővonalát. `Avg` 

   [![Metrics (Metrikák) bejegyzés – Steeltoe (Mérőszámok) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu-steeltoe.png#lightbox)
    
1. Kattintson **az eszköztár** Szűrő hozzáadása gombjára, és válassza ki, hogy csak a `App=solar-system-weather` **solar-system-weather alkalmazás cpu-használatát lássa.**

   [![Szűrő használata metrikákban – Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter-steeltoe.png#lightbox)

1. Az előző lépésben létrehozott szűrő elvetését, válassza a Felosztás alkalmazása lehetőséget, majd válassza az Értékek lehetőséget a különböző alkalmazások `App` cpu-használatának figyeléséhez. 

   [![Felosztás alkalmazása metrikákban – Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split-steeltoe.png#lightbox)

## <a name="distributed-tracing"></a>Elosztott nyomkövetés

1. A Azure Portal a szolgáltatás **| Áttekintés** lap, **majd a Figyelés** szakaszban válassza az Elosztott **nyomkövetés** lehetőséget. Ezután válassza az **Alkalmazástérkép megtekintése** lapot a jobb oldalon.

   [![Distributed Tracing (Elosztott nyomkövetés) bejegyzés – Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png#lightbox)

1. Most már láthatja az alkalmazások közötti hívások állapotát. 

   [![Elosztott nyomkövetés áttekintése – Egy adott témakörben ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview-steeltoe.png#lightbox)
    
1. A naprendszer **időjárása** és a bolygó időjárásszolgáltatója közötti kapcsolat kiválasztásával további részleteket, például **HTTP-metódusok** leglassabb hívásait láthatja.

   [![Elosztott nyomkövetés – Eredmény ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call-steeltoe.png#lightbox)
    
1. Végül válassza a **Teljesítmény vizsgálata lehetőséget** a hatékonyabb beépített teljesítményelemzés felfedezéséhez.

   [![Elosztott nyomkövetési teljesítmény – Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance-steeltoe.png#lightbox)
::: zone-end

::: zone pivot="programming-language-java"
Az alkalmazás beépített monitorozási Azure Spring Cloud összetett problémák hibakeresését és monitorozását teszi lehetővé. Azure Spring Cloud [Sleuth Spring Cloud](https://spring.io/projects/spring-cloud-sleuth) integrálható az Azure [Application Insights.](../azure-monitor/app/app-insights-overview.md) Ez az integráció hatékony naplókat, metrikákat és elosztott nyomkövetési képességeket biztosít a Azure Portal. Az alábbi eljárások ismertetik, hogyan használható a Log Streaming, a Log Analytics, a Metrics és az Elosztott nyomkövetés az üzembe helyezett PiggyMetrics-alkalmazásokkal.

## <a name="prerequisites"></a>Előfeltételek

Kövesse az előző lépéseket: 

* [A példány példányának Azure Spring Cloud](spring-cloud-quickstart-provision-service-instance.md)
* [A konfigurációs kiszolgáló beállítása](spring-cloud-quickstart-setup-config-server.md)
* [Alkalmazások fejlesztése és üzembe helyezése.](spring-cloud-quickstart-deploy-apps.md)

## <a name="logs"></a>Naplók

A naplókat kétféleképpen lehet Azure Spring Cloud **naplók** megtekintése: a valós idejű naplók alkalmazáspéldányonkénti streamelése vagy a **Log Analytics** a speciális lekérdezési képességgel rendelkező összesített naplókhoz.

### <a name="log-streaming"></a>Naplóstreamelés

#### <a name="cli"></a>[Parancssori felület](#tab/Azure-CLI)

Az Azure CLI-beli naplóstreamelést a következő paranccsal használhatja.

```azurecli
az spring-cloud app logs -s <service instance name> -g <resource group name> -n gateway -f
```

A következő naplók fognak látni:

[![Naplóstreamelés az Azure CLI-ről ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png)](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png#lightbox)

> [!TIP]
> A `az spring-cloud app logs -h` használatával további paramétereket és naplóstream-funkciókat tár fel.

#### <a name="intellij"></a>[Intellij](#tab/IntelliJ)

A naplók lekért Azure Toolkit for IntelliJ:

1. Válassza **az Azure Explorer** lehetőséget, majd **Spring Cloud** lehetőséget.

1. Kattintson a jobb gombbal a futó alkalmazásra.

1. A **legördülő listából** válassza a Streamnaplók lehetőséget.

   ![Streamnaplók kiválasztása](media/spring-cloud-intellij-howto/streaming-logs.png)
    
1. Válassza a **Példány lehetőséget.**

   ![Példány kiválasztása](media/spring-cloud-intellij-howto/select-instance.png)
    
1. A streamelési napló megjelenik a kimeneti ablakban.

   ![Streamnapló kimenete](media/spring-cloud-intellij-howto/streaming-log-output.png)

---
### <a name="log-analytics"></a>Log Analytics

1. Ugrás a szolgáltatás **| Áttekintés** lap, és **válassza a Monitorozás** szakaszban a **Naplók** lehetőséget. Kattintson **a Futtatás** gombra az egyik mintalekérdezésen a Azure Spring Cloud. 

   [![Logs Analytics-bejegyzés ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png)](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png#lightbox)
    
1. Ezután szűrt naplókat fog látni. A [lekérdezések írásával kapcsolatos](../azure-monitor/logs/get-started-queries.md) további útmutatásért tekintse meg az Azure Log Analytics-dokumentumokban található útmutatást.

   [![Logs Analytics-lekérdezés ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png)](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png#lightbox)

1. A Log Analyticsben használt lekérdezési nyelvvel kapcsolatos további információkért lásd a naplólekérdezések [Azure Monitor.](/azure/data-explorer/kusto/query/) Az összes Log Analytics-napló központi ügyfélről való lekérdezéséhez tekintse meg a [Azure Data Explorer.](https://docs.microsoft.com/azure/data-explorer/query-monitor-data)

## <a name="metrics"></a>Mérőszámok

1. Ugrás a szolgáltatás **| Áttekintés** lap, és válassza **a Metrikák** lehetőséget a **Monitorozás** szakaszban. Adja hozzá az első metrikát a Metrika és az Összesítés lehetőség kiválasztásával, hogy lássa a teljes `system.cpu.usage`  `Avg` CPU-használat idővonalát. 

   [![Metrikák bejegyzés ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png#lightbox)
    
1. Kattintson **a fenti eszköztár** Szűrő hozzáadása gombjára, és válassza a lehetőséget, hogy csak az `App=Gateway` átjáróalkalmazás cpu-használatát **lássa.**

   [![Szűrő használata metrikákban ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png#lightbox)

1. A fent létrehozott szűrő elvetését, kattintson a Felosztás **alkalmazása elemre,** és válassza az Értékek lehetőséget a különböző alkalmazások `App`  cpu-használatának figyeléhez.

   [![Felosztás alkalmazása metrikákban ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png#lightbox)

## <a name="distributed-tracing"></a>Elosztott nyomkövetés

1. Ugrás a szolgáltatás **| Áttekintés** lap, **és válassza az Elosztott nyomkövetés lehetőséget** a **Figyelés szakaszban.** Ezután kattintson az **Alkalmazástérkép megtekintése lapra** a jobb oldalon.

   [![Elosztott nyomkövetés bejegyzés ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png#lightbox)

1. Most már láthatja a Piggymetrics-alkalmazások közötti hívások állapotát. 

   [![Elosztott nyomkövetés áttekintése ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png#lightbox)
    
1. Az átjáró és **az** account-service közötti hivatkozásra kattintva további részleteket, például a **HTTP-metódusok** leglassabb hívásait láthatja.

   [![Elosztott nyomkövetés ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png#lightbox)
    
1. Végül kattintson a **Teljesítmény vizsgálata elemre** a hatékonyabb beépített teljesítményelemzés felfedezéséhez.

   [![Elosztott nyomkövetési ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png) teljesítmény](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png#lightbox)

::: zone-end

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ezekben a rövid útmutatókban olyan Azure-erőforrásokat hozott létre, amelyek továbbra is díjat halmának fel, ha az előfizetésben maradnak. Ha várhatóan nem lesz szüksége ezekre az erőforrásokra a jövőben, törölje az erőforráscsoportot a portálon vagy a következő parancs futtatásával a Cloud Shell:

```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Egy korábbi rövid útmutatóban az alapértelmezett erőforráscsoport-nevet is beállította. Ha nem folytatja a következő rövid útmutatóval, törölje az alapértelmezett beállítást a következő CLI-parancs futtatásával:

```azurecli
az configure --defaults group=
```

## <a name="next-steps"></a>Következő lépések

A szolgáltatások további monitorozási Azure Spring Cloud lásd:

> [!div class="nextstepaction"]
> [Diagnosztikai szolgáltatások](diagnostic-services.md)
>
> [Elosztott nyomkövetés](spring-cloud-howto-distributed-tracing.md)
>
> [Naplók streamelése valós időben](spring-cloud-howto-log-streaming.md)
