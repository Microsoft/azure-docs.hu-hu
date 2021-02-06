---
title: Az Azure Service Fabric Mesh szolgáltatásban futó alkalmazások automatikus méretezése
description: Megtudhatja, hogyan konfigurálhat automatikusan méretezhető házirendeket egy Service Fabric Mesh-alkalmazás szolgáltatásaihoz.
author: georgewallace
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: a707e3601bb24b2d5c2aa9402edff4a2e8803033
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625904"
---
# <a name="create-autoscale-policies-for-a-service-fabric-mesh-application"></a>Service Fabric Mesh-alkalmazáshoz tartozó autoskálázási szabályzatok létrehozása

> [!IMPORTANT]
> Az Azure Service Fabric Mesh előzetes verziója ki lett vonva. Az új központi telepítések többé nem lesznek engedélyezve a Service Fabric Mesh API-n keresztül. A meglévő központi telepítések támogatása a 2021. április 28. után folytatódik.
> 
> Részletekért lásd: az [Azure Service Fabric Mesh előzetes verziójának nyugdíjazása](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Az alkalmazások Service Fabric Meshba való üzembe helyezésének egyik fő előnye, hogy könnyedén méretezheti vagy kibővítheti szolgáltatásait. Ezt a szolgáltatások különböző terhelésének kezelésére, illetve a rendelkezésre állás javítására kell használni. A szolgáltatásokat manuálisan vagy akár ki is méretezheti, vagy beállíthatja az automatikus skálázási házirendeket.

Az [automatikus skálázás](service-fabric-mesh-scalability.md#autoscaling-service-instances) lehetővé teszi a szolgáltatási példányok számának dinamikus méretezését (horizontális skálázás). Az automatikus skálázás nagy rugalmasságot biztosít, és lehetővé teszi a szolgáltatási példányok kiépítési vagy eltávolítását a CPU vagy a memória kihasználtsága alapján.

## <a name="options-for-creating-an-auto-scaling-policy-trigger-and-mechanism"></a>Automatikus skálázási szabályzat, trigger és mechanizmus létrehozásának lehetőségei
Egy automatikus skálázási házirend van definiálva minden egyes méretezni kívánt szolgáltatáshoz. A házirend a YAML szolgáltatás-erőforrás fájljában vagy a JSON-telepítési sablonban van definiálva. Az egyes skálázási házirendek két részből állnak: egy triggerből és egy skálázási mechanizmusból.

Az eseményindító meghatározza, hogy mikor kell meghívja az automatikus skálázási szabályzatot.  Itt adhatja meg az trigger (átlagos terhelés) és a figyelni kívánt metrika (CPU vagy memória) típusát.  A felső és az alsó terhelési küszöbértékek százalékban megadva. A skálázási időköz határozza meg, hogy a rendszer milyen gyakran vizsgálja (másodpercben) a megadott kihasználtságot (például az átlagos CPU-terhelést) az összes jelenleg telepített szolgáltatási példányon.  A mechanizmus akkor aktiválódik, ha a figyelt metrika az alsó küszöbérték alá esik, vagy a felső küszöbérték fölé emelkedik.  

A skálázási mechanizmus meghatározza, hogyan kell végrehajtani a skálázási műveletet a házirend indításakor.  Adja meg a mechanizmus típusát (replika hozzáadása/eltávolítása), a minimális és a maximális replikát (egész szám).  A szolgáltatás replikáinak száma soha nem méretezhető a minimális darabszám vagy a maximális szám felett.  Adja meg a méretezési növekményt egész számként, amely a skálázási műveletben hozzáadni vagy eltávolítani kívánt replikák száma.  

## <a name="define-an-auto-scaling-policy-in-a-json-template"></a>Automatikus skálázási szabályzat definiálása JSON-sablonban

A következő példa egy automatikus skálázási szabályzatot mutat be egy JSON-telepítési sablonban.  Az automatikus skálázási szabályzatot a szolgáltatás egy tulajdonságában kell deklarálni, amelyet méretezni kell.  Ebben a példában egy CPU átlagos betöltési trigger van definiálva.  A mechanizmus akkor aktiválódik, ha az összes telepített példány átlagos CPU-terhelése 0,2 (20%) alá esik vagy 0,8 (80%) fölé kerül.  A CPU-terhelés 60 másodpercenként van bejelölve.  A skálázási mechanizmus a példányok hozzáadásához vagy eltávolításához van definiálva, ha a házirend aktiválva van.  A szolgáltatási példányok egy adott számú növekményben lesznek hozzáadva vagy eltávolítva.  A példányok minimális száma és a 40-es példányok maximális száma is meg van határozva.

```json
{
"apiVersion": "2018-09-01-preview",
"name": "WorkerApp",
"type": "Microsoft.ServiceFabricMesh/applications",
"location": "[parameters('location')]",
"dependsOn": [
"Microsoft.ServiceFabricMesh/networks/worker-app-network"
],
"properties": {
"services": [   
    { ... },       
    {
    "name": "WorkerService",
    "properties": {
        "description": "Worker Service",
        "osType": "linux",
        "codePackages": [
        {  ...              }
        ],
        "replicaCount": 1,
        "autoScalingPolicies": [
        {
            "name": "AutoScaleWorkerRule",
            "trigger": {
                "kind": "AverageLoad",
                "metric": {
                    "kind": "Resource",
                    "name": "cpu"
                },
                "lowerLoadThreshold": "0.2",
                "upperLoadThreshold": "0.8",
                "scaleIntervalInSeconds": "60"
            },
            "mechanism": {
                "kind": "AddRemoveReplica",
                "minCount": "1",
                "maxCount": "40",
                "scaleIncrement": "1"
            }
        }
        ],        
        ...
    }
    }
]
}
}
```

## <a name="define-an-autoscale-policy-in-a-serviceyaml-resource-file"></a>Az autoskálázási szabályzat definiálása egy Service. YAML-erőforrásfájl
Az alábbi példa egy Service Resource (YAML) fájlban lévő automatikus skálázási szabályzatot mutat be.  Az automatikus skálázási szabályzatot a rendszer a méretezni kívánt szolgáltatás tulajdonságának nyilvánította.  Ebben a példában egy CPU átlagos betöltési trigger van definiálva.  A mechanizmus akkor aktiválódik, ha az összes telepített példány átlagos CPU-terhelése 0,2 (20%) alá esik vagy 0,8 (80%) fölé kerül.  A CPU-terhelés 60 másodpercenként van bejelölve.  A skálázási mechanizmus a példányok hozzáadásához vagy eltávolításához van definiálva, ha a házirend aktiválva van.  A szolgáltatási példányok egy adott számú növekményben lesznek hozzáadva vagy eltávolítva.  A példányok minimális száma és a 40-es példányok maximális száma is meg van határozva.

```yaml
## Service definition ##
application:
  schemaVersion: 1.0.0-preview2
  name: WorkerApp
  properties:
    services:
      - name: WorkerService
        properties:
          description: WorkerService description.
          osType: Linux
          codePackages:
            ...
          replicaCount: 1
          autoScalingPolicies:
            - name: AutoScaleWorkerRule
              trigger:
                kind: AverageLoad
                metric:
                  kind: Resource
                  name: cpu
                lowerLoadThreshold: 0.2
                upperLoadThreshold: 0.8
                scaleIntervalInSeconds: 60
              mechanism:
                kind: AddRemoveReplica
                minCount: 1
                maxCount: 40
                scaleIncrement: 1
          ...
```

## <a name="next-steps"></a>Következő lépések
Útmutató a [szolgáltatások manuális méretezéséhez](service-fabric-mesh-tutorial-template-scale-services.md)
