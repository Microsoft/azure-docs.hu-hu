---
title: Teljesítményfigyelés Windows Azure Diagnostics
description: Az Azure Service Fabric-fürtökhöz tartozó teljesítményszámlálók gyűjtéséhez használja a Windows Azure Diagnostics.
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: 6803494d29bf97336e30128f9f5ad20ec73ce202
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627404"
---
# <a name="performance-monitoring-with-the-windows-azure-diagnostics-extension"></a>Teljesítményfigyelés a Windows Azure Diagnostics bővítménnyel

Ez a dokumentum a Windows-fürtök Windows Azure Diagnostics (WAD) bővítményének használatával a teljesítményszámlálók gyűjteményének beállításához szükséges lépéseket ismerteti. Linux-fürtök esetében állítsa be a [log Analytics-ügynököt](service-fabric-diagnostics-oms-agent.md) a csomópontok teljesítményszámlálói összegyűjtéséhez. 

 > [!NOTE]
> Ezeknek a lépéseknek a működéséhez a WAD-bővítményt a fürtön kell telepíteni. Ha nincs beállítva, az [események összesítése és gyűjtése a Windows Azure Diagnostics használatával](service-fabric-diagnostics-event-aggregation-wad.md).  


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="collect-performance-counters-via-the-wadcfg"></a>Teljesítményszámlálók gyűjtése a WadCfg keresztül

A teljesítményszámlálók WAD használatával történő összegyűjtéséhez a konfigurációt a fürt Resource Manager-sablonjában megfelelően kell módosítani. Kövesse az alábbi lépéseket egy olyan teljesítményszámláló hozzáadásához, amelyet szeretne gyűjteni a sablonhoz, és futtasson egy Resource Manager-erőforrás frissítését.

1. A WAD-konfiguráció megkeresése a fürt sablonjában – keresés `WadCfg` . A következőhöz tartozó teljesítményszámlálók hozzáadására lesz szüksége: `DiagnosticMonitorConfiguration` .

2. Állítsa be úgy a konfigurációt, hogy a teljesítményszámlálók összegyűjtéséhez a következő szakaszt adja hozzá a-hoz `DiagnosticMonitorConfiguration` . 

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": []
    }
    ```

    A meghatározza, hogy a `scheduledTransferPeriod` begyűjtött számlálók milyen gyakran kerülnek át az Azure Storage-táblába és bármely konfigurált fogadóba. 

3. Adja hozzá azokat a teljesítményszámlálókat, amelyeket szeretne gyűjteni az `PerformanceCounterConfiguration` előző lépésben deklarált módon. Minden összegyűjteni kívánt számláló a következővel van definiálva:,,, `counterSpecifier` `sampleRate` `unit` `annotation` és minden fontos `sinks` .

Íme egy példa egy olyan konfigurációra, amelynek a számlálója a *teljes processzoridő* (a CPU által a feldolgozási műveletekhez használt idő, valamint *Service Fabric színészi metódusok másodpercenkénti meghívása*, az egyik Service Fabric egyéni teljesítményszámláló. Tekintse meg a [megbízható Actor](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters) teljesítményszámlálók és a [megbízható szolgáltatások](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters) teljesítményszámlálói című témakört Service Fabric egyéni teljesítményszámláló-számlálók teljes listájáért.

 ```json
 "WadCfg": {
         "DiagnosticMonitorConfiguration": {
           "overallQuotaInMB": "50000",
           "EtwProviders": {
             "EtwEventSourceProviderConfiguration": [
               {
                 "provider": "Microsoft-ServiceFabric-Actors",
                 "scheduledTransferKeywordFilter": "1",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricReliableActorEventTable"
                 }
               },
               {
                 "provider": "Microsoft-ServiceFabric-Services",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricReliableServiceEventTable"
                 }
               }
             ],
             "EtwManifestProviderConfiguration": [
               {
                 "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                 "scheduledTransferLogLevelFilter": "Information",
                 "scheduledTransferKeywordFilter": "4611686018427387904",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricSystemEventTable"
                 }
               }
             ]
           },
           "PerformanceCounters": {
                 "scheduledTransferPeriod": "PT1M",
                 "PerformanceCounterConfiguration": [
                     {
                         "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                         "sampleRate": "PT1M",
                         "unit": "Percent",
                         "annotation": [
                         ],
                         "sinks": ""
                     },
                     {
                         "counterSpecifier": "\\Service Fabric Actor Method(*)\\Invocations/Sec",
                         "sampleRate": "PT1M",
                     }
                 ]
             }
         }
       },
  ```

 A számláló mintavételi sebessége az igényeinek megfelelően módosítható. A formátum a következő: `PT<time><unit>` , ha azt szeretné, hogy a számláló másodpercenként legyen összegyűjtve, akkor állítsa be `"sampleRate": "PT15S"` .

 Az ARM-sablonban szereplő változók használatával a teljesítményszámlálók tömbjét gyűjthetheti össze, ami hasznos lehet a teljesítményszámlálók egy folyamatból való összegyűjtésekor. Az alábbi példában folyamatban van a processzoridő és a begyűjtő adatok gyűjtési ideje, majd a csomópontokon 2 teljesítményszámláló is minden változót használ. 

 ```json
"variables": {
  "copy": [
      {
        "name": "processorTimeCounters",
        "count": "[length(parameters('monitoredProcesses'))]",
        "input": {
          "counterSpecifier": "\\Process([parameters('monitoredProcesses')[copyIndex('processorTimeCounters')]])\\% Processor Time",
          "sampleRate": "PT1M",
          "unit": "Percent",
          "sinks": "applicationInsights",
          "annotation": [
            {
              "displayName": "[concat(parameters('monitoredProcesses')[copyIndex('processorTimeCounters')],' Processor Time')]",
              "locale": "en-us"
            }
          ]
        }
      },
      {
        "name": "gcTimeCounters",
        "count": "[length(parameters('monitoredProcesses'))]",
        "input": {
          "counterSpecifier": "\\.NET CLR Memory([parameters('monitoredProcesses')[copyIndex('gcTimeCounters')]])\\% Time in GC",
          "sampleRate": "PT1M",
          "unit": "Percent",
          "sinks": "applicationInsights",
          "annotation": [
            {
              "displayName": "[concat(parameters('monitoredProcesses')[copyIndex('gcTimeCounters')],' Time in GC')]",
              "locale": "en-us"
            }
          ]
        }
      }
    ],
    "machineCounters": [
      {
        "counterSpecifier": "\\Memory\\Available Bytes",
        "sampleRate": "PT1M",
        "unit": "KB",
        "sinks": "applicationInsights",
        "annotation": [
          {
            "displayName": "Memory Available Kb",
            "locale": "en-us"
          }
        ]
      },
      {
        "counterSpecifier": "\\Memory\\% Committed Bytes In Use",
        "sampleRate": "PT15S",
        "unit": "percent",
        "annotation": [
          {
            "displayName": "Memory usage",
            "locale": "en-us"
          }
        ]
      }
    ]
  }
....
"WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "overallQuotaInMB": "50000",
      "Metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', variables('vmNodeTypeApp2Name'))]"
      },
      "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": "[concat(variables ('processorTimeCounters'), variables('gcTimeCounters'),  variables('machineCounters'))]"
      },
....
```

1. Miután hozzáadta a megfelelő teljesítményszámlálókat, amelyeket össze kell gyűjteni, frissítenie kell a fürterőforrás-t, hogy ezek a változások a futó fürtben is megjelennek. Mentse a módosított `template.json` és nyissa meg a PowerShellt. A fürtöt a használatával is frissítheti `New-AzResourceGroupDeployment` . A híváshoz az erőforráscsoport neve, a frissített sablonfájl és a parameters (paraméterek) fájl szükséges, és a rendszer felszólítja az erőforrás-kezelőt, hogy végezze el a megfelelő módosításokat a frissített erőforrásokon. Miután bejelentkezett a fiókjába, és a megfelelő előfizetéssel rendelkezik, használja a következő parancsot a frissítés futtatásához:

    ```sh
    New-AzResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

1. Ha a frissítés befejeződik (15-45 perc között tart, attól függően, hogy az első telepítés és az erőforráscsoport mérete), a WAD-nek össze kell gyűjtenie a teljesítményszámlálókat, és el kell küldenie azokat a WADPerformanceCountersTable nevű táblába a fürthöz társított Storage-fiókban. Tekintse meg a teljesítményszámlálók Application Insightsban való [hozzáadásával az AI-gyűjtőt a Resource Manager-sablonhoz](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template).

## <a name="next-steps"></a>Következő lépések
* Gyűjtsön további teljesítményszámlálókat a fürthöz. A begyűjtött számlálók listáját a [teljesítmény mérőszámai](service-fabric-diagnostics-event-generation-perf.md) részben tekintheti meg.
* A [monitorozás és diagnosztika szolgáltatás használata Windows rendszerű virtuális gépekkel és Azure Resource Manager-sablonokkal](../virtual-machines/extensions/diagnostics-template.md) további módosításokat végezhet a alkalmazásban `WadCfg` , beleértve a további Storage-fiókok konfigurálását a diagnosztikai információk küldéséhez.
* Látogasson el a [WadCfg Builder](https://azure.github.io/azure-diagnostics-tools/config-builder/) webhelyre, és győződjön meg arról, hogy a szintaxis helyes. ( https://azure.github.io/azure-diagnostics-tools/config-builder/) hozzon létre egy sablont a semmiből, és ellenőrizze, hogy helyes-e a szintaxis.
