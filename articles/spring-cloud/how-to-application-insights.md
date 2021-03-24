---
title: A Application Insights Java In-Process Agent használata az Azure Spring Cloud-ban
description: Alkalmazások és szolgáltatások figyelése Application Insights Java In-Process Agent használatával az Azure Spring Cloud-ban.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: f6f1ed1a3e09397a720dfd4d842b79cd88aa738d
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878135"
---
# <a name="application-insights-java-in-process-agent-in-azure-spring-cloud-preview"></a>Application Insights Java In-Process Agent az Azure Spring Cloud-ban (előzetes verzió)

Ez a dokumentum ismerteti, hogyan figyelhetők meg az alkalmazások és a szolgáltatások a Application Insights Java-ügynökkel az Azure Spring Cloud-ban. 

Ezzel a funkcióval a következőket teheti:

* Keresési nyomkövetési adatkeresés különböző szűrőkkel.
* A szolgáltatások függőségi térképének megtekintése.
* A kérelmek teljesítményének ellenőrzését.
* Valós idejű élő metrikák figyelése.
* A kérelmekkel kapcsolatos hibák keresése.
* Az alkalmazás metrikáinak keresése.

Application Insights számos megfigyelhető perspektívát biztosít, többek között:

* Alkalmazástérkép
* Teljesítmény
* Hibák
* Mérőszámok
* Élő metrikák
* Rendelkezésre állás

## <a name="enable-java-in-process-agent-for-application-insights"></a>A Java In-Process Agent engedélyezése Application Insights számára

Az alábbi eljárással engedélyezheti a Java In-Process Agent előzetes verzióját.

1. Nyissa meg a szolgáltatási példány szolgáltatás áttekintése lapját.
2. Kattintson **Application Insights** bejegyzés a figyelés panel alatt.
3. A **Application Insights** -integráció engedélyezéséhez kattintson **Application Insights** gombra.
4. Válasszon ki Application Insights egy meglévő példányát, vagy hozzon létre újat.
5. A **feldolgozható Java-ügynök lehetővé** teszi, hogy az előnézeti Java folyamaton belüli ügynök funkcióját engedélyezze. Itt a mintavételezési sebességet 0 és 100 között is testreszabhatja.
6.  Kattintson a **Mentés** gombra a módosítás mentéséhez.

## <a name="portal"></a>Portál

1. Ugrás a **szolgáltatásra | Áttekintés** oldalon, majd a **figyelés** szakaszban válassza a **Application Insights** lehetőséget. 
2. Az Azure Spring Cloud-ban való Application Insights engedélyezéséhez kattintson a **Application Insights engedélyezése** lehetőségre.
3. Kattintson a **Java-folyamaton belüli ügynök engedélyezése** lehetőségre a Java IPA előzetes verziójának engedélyezéséhez. Ha egy IPA-előnézeti funkció engedélyezve van, konfigurálhat egy opcionális mintavételi sebességet (alapértelmezett 10,0%).

  [![IPA 0](media/spring-cloud-application-insights/insights-process-agent-0.png)](media/spring-cloud-application-insights/insights-process-agent-0.png)

## <a name="using-the-application-insights-feature"></a>Az Application Insights funkció használata

Ha a **Application Insights** funkció engedélyezve van, a következőket teheti:

A bal oldali navigációs ablaktáblán kattintson a **Application Insights** gombra a Application Insights **Áttekintés** lapjára való ugráshoz. 

* Az alkalmazások közötti hívások állapotának megjelenítéséhez kattintson az **alkalmazás-hozzárendelés** elemre.

  [![IPA 2](media/spring-cloud-application-insights/insights-process-agent-2-map.png)](media/spring-cloud-application-insights/insights-process-agent-2-map.png)

* Kattintson az ügyfelek és szolgáltatások közötti hivatkozásra, és `petclinic` tekintse meg a további részleteket, például az SQL-lekérdezést.

* A bal oldali navigációs ablaktáblán kattintson a **teljesítmény** elemre az összes alkalmazás műveleteinek, valamint a függőségeinek és szerepköreinek megtekintéséhez.

  [![IPA 4](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)

* A bal oldali navigációs ablaktáblán kattintson a **hibák** elemre, és ellenőrizze, hogy nem várt-e az alkalmazásokból.

  [![IPA 6](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)

* A bal oldali navigációs panelen kattintson a **metrikák** elemre, és válassza ki a névteret, és ha van, akkor a Spring boot metrikáit és az egyéni metrikákat is látni fogja.

  [![IPA 7](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)

* A bal oldali navigációs panelen kattintson az **élő metrikák** elemre a különböző dimenziók valós idejű metrikáinak megtekintéséhez.

  [![IPA 8](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)

* A bal oldali navigációs ablaktáblán kattintson a **rendelkezésre állás** lehetőségre a webalkalmazások rendelkezésre állásának és rugalmasságának figyeléséhez [Application Insightsban rendelkezésre állási tesztek](../azure-monitor/app/monitor-web-app-availability.md)létrehozásával.

  [![IPA 9](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)

## <a name="arm-template"></a>ARM-sablon
A Azure Resource Manager sablon használatához másolja az alábbi tartalmat a következőre: `azuredeploy.json` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.AppPlatform/Spring",
            "name": "customize this",
            "apiVersion": "2020-07-01",
            "location": "[resourceGroup().location]",
            "resources": [
                {
                    "type": "monitoringSettings",
                    "apiVersion": "2020-11-01-preview",
                    "name": "default",
                    "properties": {
                        "appInsightsInstrumentationKey": "00000000-0000-0000-0000-000000000000",
                        "appInsightsSamplingRate": 88.0
                    },
                    "dependsOn": [
                        "[resourceId('Microsoft.AppPlatform/Spring', 'customize this')]"
                    ]
                }
            ],
            "properties": {}
        }
    ]
}
```

## <a name="cli"></a>parancssori felület
ARM-sablon alkalmazása a CLI-paranccsal:

* Meglévő Azure Spring Cloud-példány esetén:

```azurecli
az spring-cloud app-insights update [--app-insights/--app-insights-key] "assignedName" [--sampling-rate] "samplingRate" –name "assignedName" –resource-group "resourceGroupName"
```
* Újonnan létrehozott Azure Spring Cloud-példány esetén:

```azurecli
az spring-cloud create/update [--app-insights]/[--app-insights-key] "assignedName" --disable-app-insights false --enable-java-agent true --name "assignedName" –resource-group "resourceGroupName"
```
* Az alkalmazás-betekintés letiltása:

```azurecli
az spring-cloud app-insights update --disable –name "assignedName" –resource-group "resourceGroupName"

```

## <a name="see-also"></a>Lásd még
* [Elosztott nyomkövetés használata az Azure Spring Cloud használatával](spring-cloud-howto-distributed-tracing.md)
* [Naplók és metrikák elemzése](diagnostic-services.md)
* [Naplók streamelése valós időben](spring-cloud-howto-log-streaming.md)