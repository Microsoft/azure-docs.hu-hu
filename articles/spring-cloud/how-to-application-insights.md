---
title: A Java Application Insights In-Process használata a Azure Spring Cloud
description: Alkalmazások és mikroszolgáltatások figyelése Application Insights Java In-Process Agent használatával a Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/04/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: c7083cb6669d7bc779a8e69babfef38988819f8c
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483773"
---
# <a name="application-insights-java-in-process-agent-in-azure-spring-cloud-preview"></a>Application Insights Java In-Process Agent in Azure Spring Cloud (előzetes verzió)

Ez a dokumentum azt ismerteti, hogyan figyelheti az alkalmazásokat és a mikroszolgáltatásokat a Application Insights Java-ügynökkel a Azure Spring Cloud. 

Ezzel a funkcióval a következő funkciókat használhatja:

* Nyomkövetési adatok keresése különböző szűrőkkel.
* Mikroszolgáltatások függőségi térképének megtekintése.
* Ellenőrizze a kérések teljesítményét.
* Valós idejű élő metrikák figyelése.
* Ellenőrizze a kéréshibákat.
* Alkalmazásmetrikák ellenőrzése.

Application Insights sok megfigyelhető perspektívát biztosítanak, beleértve a következőket:

* Alkalmazástérkép
* Teljesítmény
* Hibák
* Mérőszámok
* Élő metrikák
* Rendelkezésre állás

## <a name="enable-java-in-process-agent-for-application-insights"></a>Enable Java In-Process Agent for Application Insights

Engedélyezze a Java In-Process Agent előzetes verziójú funkcióját az alábbi eljárással.

1. Ugrás a szolgáltatáspéldány szolgáltatásáttekintő oldalára.
2. Kattintson **Application Insights** elemre a Figyelés panelen.
3. Kattintson **a Application Insights** engedélyezése gombra a Application Insights engedélyezéséhez. 
4. Válasszon ki egy meglévő példányt a Application Insights vagy hozzon létre egy újat.
5. Enable **Java in-process agent** to enable preview Java in-process agent (Java in-process ügynök funkció engedélyezése a java in-process ügynök funkció engedélyezéséhez). Itt a mintavételezési sebességet is testreszabhatja 0 és 100 között.
6.  Kattintson a **Mentés** gombra a módosítás mentéséhez.

## <a name="portal"></a>Portál

1. Ugrás a szolgáltatás **| Áttekintés** **lap,** és Application Insights a Figyelés **szakaszban.** 
2. Kattintson **az Enable Application Insights** (Application Insights engedélyezése) elemre a Application Insights engedélyezéséhez Azure Spring Cloud.
3. Kattintson **a Java in-process agent engedélyezése elemre** a Java IPA előzetes verziójú funkció engedélyezéséhez. Ha az IPA előzetes verziójú funkciója engedélyezve van, konfigurálhat egy választható mintavételezési sebességet (alapértelmezés szerint 10,0%).

  [![IPA 0](media/spring-cloud-application-insights/insights-process-agent-0.png)](media/spring-cloud-application-insights/insights-process-agent-0.png)

## <a name="using-the-application-insights-feature"></a>A Application Insights használata

Ha az **Application Insights** funkció engedélyezve van, a következőt teszi lehetővé:

A bal oldali navigációs panelen kattintson a  **Application Insights** elemre a lap Áttekintés Application Insights. 

* Kattintson **az Alkalmazástérkép elemre** az alkalmazások közötti hívások állapotának megtekintése érdekében.

  [![IPA 2](media/spring-cloud-application-insights/insights-process-agent-2-map.png)](media/spring-cloud-application-insights/insights-process-agent-2-map.png)

* Kattintson a customers-service (ügyfelek szolgáltatása) közötti hivatkozásra, és további részleteket, például egy `petclinic` SQL-lekérdezést láthat.

* A bal oldali navigációs panelen kattintson a **Teljesítmény** elemre az összes alkalmazásművelet teljesítményadatának, valamint a függőségek és szerepkörök végrehajtásához.

  [![IPA 4](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)

* A bal oldali navigációs panelen kattintson a **Hibák elemre,** és nézze meg, hogy nem történt-e valami váratlan az alkalmazásokban.

  [![IPA 6](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)

* A bal oldali navigációs  panelen kattintson a Metrikák elemre, és válassza ki a névteret. Ha van ilyen, Spring Boot metrikákat és egyéni metrikákat is látni fog.

  [![IPA 7](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)

* A bal oldali navigációs panelen kattintson az **Élő** metrikák elemre a különböző dimenziók valós idejű metrikainak a megjelenítése érdekében.

  [![IPA 8](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)

* A bal oldali navigációs  panelen kattintson a Rendelkezésre állás elemre a webalkalmazások rendelkezésre állásának és válaszkészségének figyelése érdekében rendelkezésre állási tesztek létrehozásával a [Application Insights.](../azure-monitor/app/monitor-web-app-availability.md)

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

* Meglévő Azure Spring Cloud példányhoz:

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
* [Elosztott nyomkövetés használata a Azure Spring Cloud](spring-cloud-howto-distributed-tracing.md)
* [Naplók és metrikák elemzése](diagnostic-services.md)
* [Naplók streamelése valós időben](spring-cloud-howto-log-streaming.md)
