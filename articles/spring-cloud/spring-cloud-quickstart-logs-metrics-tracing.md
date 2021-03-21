---
title: Rövid útmutató – az Azure Spring Cloud apps naplóival, metrikákkal és nyomkövetéssel való monitorozása
description: A log streaming, a log Analytics, a metrikák és a nyomkövetés használatával figyelheti a Piggymetrics-minta alkalmazásokat az Azure Spring Cloud-on.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/04/2020
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: a935b34c4fcebe9128d8af0316658072e20ddfbe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100596205"
---
# <a name="quickstart-monitoring-azure-spring-cloud-apps-with-logs-metrics-and-tracing"></a>Gyors útmutató: Azure Spring Cloud-alkalmazások figyelése naplókkal, metrikákkal és nyomkövetéssel

::: zone pivot="programming-language-csharp"
Az Azure Spring Cloud beépített figyelési funkciójával az összetett problémák hibakeresését és figyelését végezheti el. Az Azure Spring Cloud integrálja a Steeltoe [elosztott nyomkövetését](https://steeltoe.io/docs/3/tracing/distributed-tracing) az Azure [Application Insightsával](../azure-monitor/app/app-insights-overview.md). Ez az integráció hatékony naplókat, metrikákat és elosztott nyomkövetési képességet biztosít a Azure Portalból.

Az alábbi eljárások azt ismertetik, hogyan használhatók a naplózási adatfolyamok, a Log Analytics, a metrikák és az elosztott nyomkövetés az előző rövid útmutatókba telepített minta alkalmazással.

## <a name="prerequisites"></a>Előfeltételek

* A sorozat előző rövid útmutatóinak elvégzése:

  * [Azure Spring Cloud-szolgáltatás kiépítése](spring-cloud-quickstart-provision-service-instance.md).
  * Az [Azure Spring Cloud konfigurációs kiszolgáló beállítása](spring-cloud-quickstart-setup-config-server.md).
  * [Alkalmazások készítése és üzembe helyezése](spring-cloud-quickstart-deploy-apps.md).

## <a name="logs"></a>Naplók

Az Azure Spring Cloud naplóit kétféleképpen tekintheti meg: valós idejű naplók **naplózása** alkalmazás-példányon vagy **log Analytics** a speciális lekérdezési képességgel rendelkező összesített naplókhoz.

### <a name="log-streaming"></a>Naplózási adatfolyam

A log streaming az Azure CLI-ben a következő paranccsal használható.

```azurecli
az spring-cloud app logs -n solar-system-weather -f
```

A következő példához hasonló kimenet jelenik meg:

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
> `az spring-cloud app logs -h`A segítségével további paramétereket és a log stream működését is megismerheti.

### <a name="log-analytics"></a>Log Analytics

1. A Azure Portal lépjen a **szolgáltatáshoz | Áttekintés** oldalon, majd a **figyelés** szakaszban válassza a **naplók** lehetőséget. Válassza a **Futtatás** lehetőséget az Azure Spring Cloud-beli minta-lekérdezések egyikén.

   [![Naplók Analytics-bejegyzés ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png)](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png#lightbox)
    
1. Szerkessze a lekérdezést, és távolítsa el azokat a WHERE záradékokat, amelyek a figyelmeztetési és a hibajelentési naplókra korlátozzák a megjelenítést.

1. Ezután válassza `Run` a () lehetőséget, és látni fogja a naplókat. A lekérdezések írásához további útmutatást az [Azure log Analytics dokumentációjában](../azure-monitor/logs/get-started-queries.md) talál.

   [![Naplók Analytics-lekérdezés – Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query-steeltoe.png#lightbox)

## <a name="metrics"></a>Mérőszámok

1. A Azure Portal lépjen a **szolgáltatáshoz | Áttekintés** lapon válassza a **metrikák** lehetőséget a **figyelés** szakaszban. Adja hozzá első mérőszámát úgy, hogy kiválasztja a **teljesítmény (.net)** vagy a **kérelem (.NET)** alatti .net-mérőszámok egyikét a **metrika** legördülő menüből, valamint az `Avg` **összesítést** az adott metrika idővonalának megtekintéséhez.

   [![Metrikai bejegyzés – Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu-steeltoe.png#lightbox)
    
1. Kattintson a **szűrő hozzáadása** elemre az eszköztáron, és válassza a `App=solar-system-weather` CPU-használat csak a **nap-rendszer időjárási** alkalmazáshoz lehetőséget.

   [![Szűrő használata mérőszámokban – Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter-steeltoe.png#lightbox)

1. Zárja be az előző lépésben létrehozott szűrőt, válassza a **felosztás alkalmazása** lehetőséget, majd válassza `App` az **értékek** lehetőséget a különböző alkalmazások CPU-használatának megjelenítéséhez.

   [![Felosztás alkalmazása mérőszámokban – Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split-steeltoe.png#lightbox)

## <a name="distributed-tracing"></a>Elosztott nyomkövetés

1. A Azure Portal lépjen a **szolgáltatáshoz | Áttekintés** lapon válassza az **elosztott nyomkövetés** lehetőséget a **figyelés** szakaszban. Ezután kattintson a jobb oldalon található **alkalmazás-hozzárendelés megtekintése** fülre.

   [![Elosztott nyomkövetési bejegyzés – Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png#lightbox)

1. Mostantól megtekintheti a hívások állapotát az alkalmazások között. 

   [![Elosztott nyomkövetés áttekintése – Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview-steeltoe.png#lightbox)
    
1. Válassza a **Solar-System-Weather** és a **Planet-Weather-Provider** közötti kapcsolatot, ha további részleteket szeretne látni, például a leglassabb hívásokat http-módszerekkel.

   [![Elosztott nyomkövetés – Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call-steeltoe.png#lightbox)
    
1. Végül a **teljesítmény vizsgálata** lehetőségre kattintva megismerheti a nagyobb teljesítményű beépített teljesítményt.

   [![Elosztott nyomkövetési teljesítmény – Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance-steeltoe.png#lightbox)
::: zone-end

::: zone pivot="programming-language-java"
Az Azure Spring Cloud beépített figyelési funkciójával az összetett problémák hibakeresését és figyelését végezheti el. Az Azure Spring Cloud egyesíti a [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) az Azure [Application Insightsával](../azure-monitor/app/app-insights-overview.md). Ez az integráció hatékony naplókat, metrikákat és elosztott nyomkövetési képességet biztosít a Azure Portalból. Az alábbi eljárások azt ismertetik, hogyan használhatók a naplózási adatfolyamok, a Log Analytics, a metrikák és az elosztott nyomkövetés a telepített PiggyMetrics-alkalmazásokkal.

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre az előző lépéseket: 

* [Azure Spring Cloud-példány kiépítése](spring-cloud-quickstart-provision-service-instance.md)
* [A konfigurációs kiszolgáló beállítása](spring-cloud-quickstart-setup-config-server.md)
* [Alkalmazások készítése és üzembe helyezése](spring-cloud-quickstart-deploy-apps.md).

## <a name="logs"></a>Naplók

Az Azure Spring Cloud naplóit kétféleképpen tekintheti meg: valós idejű naplók **naplózása** alkalmazás-példányon vagy **log Analytics** a speciális lekérdezési képességgel rendelkező összesített naplókhoz.

### <a name="log-streaming"></a>Naplózási adatfolyam

#### <a name="cli"></a>[Parancssori felület](#tab/Azure-CLI)

A log streaming az Azure CLI-ben a következő paranccsal használható.

```azurecli
az spring-cloud app logs -s <service instance name> -g <resource group name> -n gateway -f
```

Ekkor a következőhöz hasonló naplók láthatók:

[![Folyamatos naplózás az Azure CLI ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png) -ből](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png#lightbox)

> [!TIP]
> `az spring-cloud app logs -h`A használatával több paramétert és a log stream funkcióit is megismerheti.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

Naplók beszerzése Azure Toolkit for IntelliJ használatával:

1. Válassza az **Azure Explorer**, majd a **Spring Cloud** elemet.

1. Kattintson a jobb gombbal a futó alkalmazásra.

1. Válassza a **folyamatos átviteli naplók** lehetőséget a legördülő listából.

   ![Adatfolyam-naplók kiválasztása](media/spring-cloud-intellij-howto/streaming-logs.png)
    
1. Válassza a **példány** lehetőséget.

   ![Példány kiválasztása](media/spring-cloud-intellij-howto/select-instance.png)
    
1. A folyamatos átviteli napló a kimeneti ablakban jelenik meg.

   ![Adatfolyam-napló kimenete](media/spring-cloud-intellij-howto/streaming-log-output.png)

---
### <a name="log-analytics"></a>Log Analytics

1. Ugrás a **szolgáltatásra | Áttekintés** oldalon, majd a **figyelés** szakaszban válassza a **naplók** lehetőséget. Kattintson a **Futtatás** elemre az Azure Spring Cloud-beli lekérdezések egyikén. 

   [![Naplók Analytics-bejegyzés ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png)](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png#lightbox)
    
1. Ezután megjelennek a szűrt naplók. A lekérdezések írásához további útmutatást az [Azure log Analytics dokumentációjában](../azure-monitor/logs/get-started-queries.md) talál.

   [![Naplók Analytics-lekérdezés ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png)](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png#lightbox)

## <a name="metrics"></a>Mérőszámok

1. Ugrás a **szolgáltatásra | Áttekintés** lapon válassza a **metrikák** lehetőséget a **figyelés** szakaszban. Adja hozzá az első mérőszámot a metrika lehetőség kiválasztásával `system.cpu.usage` , és  `Avg` a teljes CPU-használat idővonalának megjelenítéséhez. 

   [![Metrikák bejegyzése ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png#lightbox)
    
1. A fenti eszköztár **szűrő hozzáadása** gombjára kattintva `App=Gateway` megtekintheti a CPU-használatot csak az **átjáró** alkalmazás esetében.

   [![Szűrő használata mérőszámokban ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png#lightbox)

1. Zárja be a fent létrehozott szűrőt, kattintson a **felosztás alkalmazása** elemre, és válassza `App` az **értékek** lehetőséget a különböző alkalmazások CPU-használatának megjelenítéséhez.

   [![Felosztás alkalmazása mérőszámokban ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png#lightbox)

## <a name="distributed-tracing"></a>Elosztott nyomkövetés

1. Ugrás a **szolgáltatásra | Áttekintés** lapon válassza az **elosztott nyomkövetés** lehetőséget a **figyelés** szakaszban. Ezután kattintson a jobb oldalon található **alkalmazás-hozzárendelés megtekintése** fülre.

   [![Elosztott nyomkövetési bejegyzés ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png#lightbox)

1. Mostantól megtekintheti a Piggymetrics-alkalmazások közötti hívások állapotát. 

   [![Elosztott nyomkövetés áttekintése ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png#lightbox)
    
1. Az **átjáró** és a **fiók** közötti hivatkozásra kattintva további részleteket tekinthet meg, például a leglassabb hívásokat http-módszerekkel.

   [![Elosztott nyomkövetés ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png#lightbox)
    
1. Végül kattintson a **teljesítmény vizsgálata** lehetőségre a nagyobb teljesítményű beépített teljesítmény-elemzések megismeréséhez.

   [![Elosztott nyomkövetési teljesítmény ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png#lightbox)

::: zone-end

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ezekben a gyors útmutatókban olyan Azure-erőforrásokat hozott létre, amelyek továbbra is felhalmozzák a díjakat, ha az előfizetésben maradnak. Ha nem várható, hogy a jövőben szüksége lenne ezekre az erőforrásokra, törölje az erőforráscsoportot a portál használatával, vagy futtassa a következő parancsot a Cloud Shellban:

```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Egy korábbi rövid útmutatóban az erőforráscsoport alapértelmezett nevét is megadhatja. Ha nem kívánja folytatni a következő rövid útmutatót, törölje az alapértelmezett beállítást az alábbi CLI-parancs futtatásával:

```azurecli
az configure --defaults group=
```

## <a name="next-steps"></a>Következő lépések

Az Azure Spring Cloud további figyelési képességeinek megismeréséhez lásd:

> [!div class="nextstepaction"]
> [Diagnosztikai szolgáltatások](diagnostic-services.md)
>
> [Elosztott nyomkövetés](spring-cloud-tutorial-distributed-tracing.md)
>
> [Naplók streamelése valós időben](spring-cloud-howto-log-streaming.md)
