---
title: Health Integration bevezetése – Azure Deployment Manager
description: Ismerteti, hogyan helyezhet üzembe egy szolgáltatást több régióban az Azure Deployment Manager használatával. A biztonságos üzembe helyezési eljárásokat mutatja be a telepítés stabilitásának ellenőrzéséhez, mielőtt az összes régióba bekerül.
author: mumian
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: jgao
ms.openlocfilehash: ae95269dbac3ef1561e19d4b7ea5dd383c1eed73
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99536967"
---
# <a name="introduce-health-integration-rollout-to-azure-deployment-manager-public-preview"></a>Állapot-integráció bevezetése az Azure Deployment Manager (nyilvános előzetes verzió)

Az [Azure Deployment Manager](./deployment-manager-overview.md) lehetővé teszi Azure Resource Manager erőforrások szakaszos bevezetésének végrehajtását. Az erőforrások régiónként vannak üzembe helyezve egy megrendelt módon. Az Azure Deployment Manager integrált állapot-ellenőrzésével nyomon követheti a bevezetéseket, és automatikusan leállíthatja a problémás bevezetéseket, így elháríthatja és csökkentheti a hatás méretezését. Ezzel a szolgáltatással csökkenthető a frissítések miatti regressziós szolgáltatás által okozott elérhetetlenség.

## <a name="health-monitoring-providers"></a>Állapotfigyelő szolgáltatók

Ahhoz, hogy a lehető legkönnyebben elérhető legyen az állapot-integráció, a Microsoft a legfelső szintű Service Health monitoring vállalatokkal együttműködve egyszerű másolási/beillesztési megoldást biztosít az állapot-ellenőrzéseknek az üzembe helyezésekkel való integrálásához. Ha még nem használja az állapotfigyelő szolgáltatás használatát, ezek nagyszerű megoldást jelentenek:

| ![Azure Deployment Manager állapotfigyelő szolgáltató Azure monitor](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-azure-monitor.svg)| ![Az Azure Deployment Manager állapotfigyelő szolgáltatójának datadoggal](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-datadog.svg) | ![Az Azure Deployment Manager állapotfigyelő szolgáltatójának site24x7](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-site24x7.svg) | ![Az Azure Deployment Manager állapotfigyelő szolgáltatójának Wavefront](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-wavefront.svg) |
|-----|-----|------|------|
|Azure Monitor, a Microsoft teljes körű megfigyelhető platformja a felhőalapú natív & a hibrid monitorozás és elemzés érdekében. |Datadoggal, a vezető monitorozási és elemzési platform a modern felhőalapú környezetekhez. Ismerje meg [, hogyan integrálható az datadoggal az Azure Deployment Managerval](https://www.datadoghq.com/azure-deployment-manager/).|Site24x7, a teljes körű magán-és nyilvános Cloud Services-figyelési megoldás. Ismerje meg [, hogyan integrálható az Site24x7 az Azure Deployment Managerval](https://www.site24x7.com/azure/adm.html).| Wavefront, a többfelhős alkalmazás-környezetek figyelési és elemzési platformja. Ismerje meg [, hogyan integrálható az Wavefront az Azure Deployment Managerval](https://go.wavefront.com/wavefront-adm/).|

## <a name="how-service-health-is-determined"></a>A szolgáltatás állapotának meghatározása

Az állapotfigyelő [szolgáltatók](#health-monitoring-providers) számos módszert kínálnak a szolgáltatások figyelésére és a szolgáltatás állapotával kapcsolatos problémák riasztására. [Azure monitor](../../azure-monitor/overview.md) példa erre az ajánlatra. Azure Monitor használatával riasztásokat hozhat létre bizonyos küszöbértékek túllépése esetén. A memória és a CPU kihasználtsága például a várt szinteken túl, amikor új frissítést telepít a szolgáltatásba. Ha értesítést küld, megteheti a kijavítani kívánt műveleteket.

Ezek az állapot-szolgáltatók jellemzően REST API-kat kínálnak, így a szolgáltatás figyelők állapota programozott módon megvizsgálható. A REST API-k vagy visszatérhetnek egy egyszerű, kifogástalan/nem kifogástalan állapotú jellel (a HTTP-válasz kódja alapján), és/vagy a kapott jelekkel kapcsolatos részletes információkkal.

Az `healthCheck` Azure Deployment Manager új lépése lehetővé teszi, hogy deklarálja az egészséges szolgáltatást jelző http-kódokat. Összetett REST-eredmények esetén olyan reguláris kifejezéseket adhat meg, amelyek egyezés esetén megfelelő állapotot jeleznek.

Az Azure Deployment Manager Health ellenőrzésének beállításához szükséges folyamat:

1. Hozzon létre egészségügyi figyelőket egy Ön által választott állapotfigyelő szolgáltatón keresztül.
1. Hozzon létre egy vagy több `healthCheck` lépést az Azure Deployment Manager bevezetésének részeként. Töltse ki a `healthCheck` lépéseket a következő információkkal:

    1. Az állapotfigyelő figyelő REST APIjának URI-ja (az állapotfigyelő szolgáltató által definiált módon).
    1. Hitelesítő adatok. Jelenleg csak az API-kulcs stílusú hitelesítés támogatott. Azure Monitor esetén a hitelesítési típust úgy kell beállítani, hogy `RolloutIdentity` Az Azure Deployment Manager bevezetéséhez használt, felhasználó által hozzárendelt felügyelt identitás kiterjessze Azure Monitorre.
    1. [Http-állapotkódok](https://www.wikipedia.org/wiki/List_of_HTTP_status_codes) vagy reguláris kifejezések, amelyek egészséges választ határoznak meg. Olyan reguláris kifejezéseket is megadhat, amelyeknek meg kell egyezniük ahhoz, hogy a válasz kifogástalan legyen, vagy olyan kifejezéseket is megadhat, amelyeknek meg kell egyezniük a válasz állapotának megfelelő állapottal. Mindkét módszer támogatott.

    Az alábbi JSON-példa az Azure Deployment Manager Azure Monitor integrálására szolgál. A példa `RolloutIdentity` egy állapot-ellenőrzési szolgáltatást használ, és megállapítja, hogy a bevezetést hogyan lehet bevezetni, ha nincsenek riasztások. Az egyetlen támogatott Azure Monitor API: [riasztások – az összes beolvasása](/rest/api/monitor/alertsmanagement/alerts/getall).

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT1M",
          "maxElasticDuration": "PT1M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "RolloutIdentity"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "\"value\":\\[\\]"
                    ],
                    "matchQuantifier": "All"
                  }
                }
              }
            ]
          }
        }
      }
    }
    ```

    A következő JSON példa az összes többi állapotfigyelő szolgáltatóra:

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT0M",
          "maxElasticDuration": "PT0M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "ApiKey",
                    "name": "code",
                    "in": "Query",
                    "value": "[parameters('healthCheckAuthAPIKey')]"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "Status: healthy",
                      "Status: warning"
                    ],
                    "matchQuantifier": "Any"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

1. Hívja `healthCheck` meg a lépéseket az Azure-Deployment Manager bevezetésének megfelelő időpontjában. Az alábbi példában egy `healthCheck` lépést hív meg a következőben: `postDeploymentSteps` `stepGroup2` .

    ```json
    "stepGroups": [
        {
            "name": "stepGroup1",
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup2",
            "dependsOnStepGroups": ["stepGroup1"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit1.name)]",
            "postDeploymentSteps": [
                {
                    "stepId": "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
                }
            ]
        },
        {
            "name": "stepGroup3",
            "dependsOnStepGroups": ["stepGroup2"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup4",
            "dependsOnStepGroups": ["stepGroup3"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit1.name)]",
            "postDeploymentSteps": []
        }
    ]
    ```

A következő témakörben talál egy példát [: oktatóanyag: az állapot-ellenőrzési szolgáltatás használata az Azure Deployment Managerban](./deployment-manager-tutorial-health-check.md).

## <a name="phases-of-a-health-check"></a>Állapot-ellenőrzési fázisok

Ezen a ponton az Azure Deployment Manager tudja, hogyan kérdezheti le a szolgáltatás állapotát, és hogy milyen fázisokban történik a bevezetés. Az Azure Deployment Manager azonban az ellenőrzések időzítésének mélyreható konfigurálását is lehetővé teszi. Egy `healthCheck` lépés végrehajtása három szekvenciális fázisban történik, amelyek mindegyike konfigurálható időtartammal rendelkezik:

1. Várakozás

    1. Egy üzembe helyezési művelet befejezése után a virtuális gépek újraindulnak, újrakonfigurálják az új adatforrások alapján, vagy akár első alkalommal is elindítják őket. Azt is időt vesz igénybe, hogy a szolgáltatások elindítsák az egészségügyi jeleket, amelyeket az állapotfigyelő szolgáltatónak hasznosnak kell lennie. Ez a viharos folyamat során nem feltétlenül érdemes megkeresni a szolgáltatás állapotát, mert a frissítés még nem érte el a stabil állapotot. A szolgáltatás valójában a kifogástalan állapotú és a nem megfelelő állapotok között ingadozik az erőforrások rendezése során.
    1. A várakozási fázisban a szolgáltatás állapota nincs figyelve. Ezzel lehetővé válik, hogy a központilag telepített erőforrások az állapot-ellenőrzési folyamat megkezdése előtt is sütni legyenek.

1. Rugalmas

    1. Mivel nem lehet tudni, hogy mennyi időt vesz igénybe az erőforrások stabilitása, a rugalmas fázis lehetővé teszi a rugalmas időszakot, ha az erőforrások potenciálisan instabilak, és ha egy kifogástalan állapotot kell fenntartaniuk.
    1. A rugalmas fázis megkezdése után az Azure Deployment Manager rendszeresen megkezdi a szolgáltatás állapotának lekérdezését a megadott REST-végponton. A lekérdezési időköz konfigurálható.
    1. Ha az állapotfigyelő olyan jeleket tartalmaz, amelyek jelzik, hogy a szolgáltatás állapota nem megfelelő, akkor a rendszer figyelmen kívül hagyja ezeket a jeleket, a rugalmas fázis folytatódik, és folytatja a lekérdezést.
    1. Ha az állapotfigyelő jelzi, hogy a szolgáltatás kifogástalan állapotú, a rugalmas fázis véget ér, és megkezdődik a HealthyState fázis.
    1. Így a rugalmas fázishoz megadott időtartam azt a maximális időt adja meg, ameddig a szolgáltatás állapotának lekérdezése az egészséges válasz megadása előtt elvégezhető.

1. HealthyState

    1. A HealthyState fázisban a szolgáltatás állapotának lekérdezése a rugalmas fázissal megegyező időközönként történik.
    1. A szolgáltatásnak meg kell őriznie az egészséges jeleket az állapotfigyelő szolgáltatótól a teljes megadott időtartamra.
    1. Ha a rendszer egy nem Kifogástalan állapotra vonatkozó választ észlel, az Azure Deployment Manager leállítja a teljes bevezetést, és visszaküldi a nem megfelelő állapotú szolgáltatási jeleket eredményező REST-választ.
    1. A HealthyState időtartamának befejeződése után a befejeződött `healthCheck` , és a telepítés a következő lépéssel folytatódik.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan integrálhatja az állapotfigyelő szolgáltatásait az Azure Deployment Managerba. A következő cikkből megtudhatja, hogyan helyezheti üzembe a Deployment Manager használatával.

> [!div class="nextstepaction"]
> [Oktatóanyag: az állapot-ellenőrzési szolgáltatás használata az Azure-ban Deployment Manager](./deployment-manager-tutorial-health-check.md)
