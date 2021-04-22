---
title: A Java Application Insights ügynök In-Process használata a Azure Spring Cloud
description: Alkalmazások és mikroszolgáltatások figyelése az Application Insights Java In-Process Agent használatával a Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/04/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: b704d2cf8e2cc8e6cf5d8049290379dd45e26737
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870336"
---
# <a name="application-insights-java-in-process-agent-in-azure-spring-cloud-preview"></a>Application Insights Java In-Process Agent a Azure Spring Cloud (előzetes verzió)

Ez a dokumentum azt ismerteti, hogyan figyelheti az alkalmazásokat és a mikroszolgáltatásokat a Application Insights Java-ügynökkel a Azure Spring Cloud. 

Ezzel a funkcióval a következő funkciókat használhatja:

* Nyomkövetési adatok keresése különböző szűrőkkel.
* Mikroszolgáltatások függőségi térképének megtekintése.
* Ellenőrizze a kérések teljesítményét.
* Valós idejű élő metrikák figyelése.
* Ellenőrizze a kéréshibákat.
* Alkalmazásmetrikák ellenőrzése.

Application Insights, többek között az alábbiakat biztosítják:

* Alkalmazástérkép
* Teljesítmény
* Hibák
* Mérőszámok
* Élő metrikák
* Rendelkezésre állás

## <a name="enable-java-in-process-agent-for-application-insights"></a>Enable Java In-Process Agent for Application Insights

Engedélyezze a Java In-Process Agent előzetes verziójú funkcióját a következő eljárással.

1. Ugrás a szolgáltatáspéldány szolgáltatásáttekintő oldalára.
2. A **Application Insights** panelen kattintson az Application Insights elemre.
3. Kattintson **a Application Insights** engedélyezése gombra a Application Insights engedélyezéséhez. 
4. Válasszon ki egy meglévő példányt a Application Insights vagy hozzon létre egy újat.
5. Enable **Java in-process agent** to enable preview Java in-process agent feature (Java in-process agent előzetes verziójú ügynök funkció engedélyezéséhez). Itt 0 és 100 között testre szabhatja a mintavételezési sebességet.
6.  Kattintson a **Mentés** gombra a módosítás mentéséhez.

## <a name="portal"></a>Portál

1. Ugrás a szolgáltatás **| Áttekintés** **lap,** és Application Insights a Figyelés **szakaszban.** 
2. Kattintson **az Enable Application Insights** (Application Insights engedélyezése) elemre a Application Insights engedélyezéséhez Azure Spring Cloud.
3. Kattintson **a Java in-process agent engedélyezése lehetőségre** a Java IPA előzetes verziójú funkció engedélyezéséhez. Ha az IPA előzetes verziójú funkciója engedélyezve van, konfigurálhat egy választható mintavételezési sebességet (alapértelmezés szerint 10,0%).

  [![IPA 0](media/spring-cloud-application-insights/insights-process-agent-0.png)](media/spring-cloud-application-insights/insights-process-agent-0.png)

## <a name="using-the-application-insights-feature"></a>A Application Insights használata

Ha **a Application Insights** funkció engedélyezve van, a következőt teszi lehetővé:

A bal oldali navigációs panelen kattintson a **Application Insights** elemre, hogy az áttekintés **áttekintési** Application Insights. 

* Kattintson **az Alkalmazástérkép elemre** az alkalmazások közötti hívások állapotának megtekintése érdekében.

  [![IPA 2](media/spring-cloud-application-insights/insights-process-agent-2-map.png)](media/spring-cloud-application-insights/insights-process-agent-2-map.png)

* Kattintson a customers-service közötti hivatkozásra, és további részleteket, például egy `petclinic` SQL-lekérdezést láthat.

* A bal oldali navigációs panelen kattintson a **Teljesítmény** elemre az összes alkalmazásművelet, valamint a függőségek és szerepkörök teljesítményadatának a megtekintése érdekében.

  [![IPA 4](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)

* A bal oldali navigációs panelen kattintson a **Hibák** elemre, és nézze meg, hogy nem történt-e valami váratlan az alkalmazásokban.

  [![IPA 6](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)

* A bal oldali navigációs  panelen kattintson a Metrikák elemre, és válassza ki a névteret, és látni fogja a Spring Boot metrikákat és az egyéni metrikákat is, ha vannak.

  [![IPA 7](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)

* A bal oldali navigációs panelen kattintson az **Élő** metrikák elemre a különböző dimenziók valós idejű metrikainak a megjelenítése érdekében.

  [![IPA 8](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)

* A bal oldali navigációs  panelen kattintson a Rendelkezésre állás elemre a webalkalmazások rendelkezésre állásának és válaszkészségének figyelése érdekében a rendelkezésre állási tesztek létrehozásával [a Application Insights.](../azure-monitor/app/monitor-web-app-availability.md)

  [![IPA 9](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)

## <a name="arm-template"></a>ARM-sablon
A sablon Azure Resource Manager másolja a következő tartalmat a `azuredeploy.json` fájlba.

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

* Meglévő adatbázispéldány Azure Spring Cloud:

```azurecli
az spring-cloud app-insights update [--app-insights/--app-insights-key] "assignedName" [--sampling-rate] "samplingRate" â€“name "assignedName" â€“resource-group "resourceGroupName"
```
* Újonnan létrehozott Azure Spring Cloud:

```azurecli
az spring-cloud create/update [--app-insights]/[--app-insights-key] "assignedName" --disable-app-insights false --enable-java-agent true --name "assignedName" â€“resource-group "resourceGroupName"
```
* Az app-insight letiltása:

```azurecli
az spring-cloud app-insights update --disable â€“name "assignedName" â€“resource-group "resourceGroupName"

```

## <a name="see-also"></a>Lásd még
* [Elosztott nyomkövetés használata Azure Spring Cloud](spring-cloud-howto-distributed-tracing.md)
* [Naplók és metrikák elemzése](diagnostic-services.md)
* [Naplók streamelése valós időben](spring-cloud-howto-log-streaming.md)
