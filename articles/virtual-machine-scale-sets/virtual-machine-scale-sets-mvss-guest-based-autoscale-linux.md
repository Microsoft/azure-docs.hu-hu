---
title: Az Azure autoscale használata vendég metrikákkal egy Linux-méretezési csoport sablonjában
description: Megtudhatja, hogyan méretezhető a vendég mérőszámok használata egy linuxos virtuálisgép-méretezési csoport sablonjában
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 04/26/2019
ms.reviewer: avverma
ms.custom: avverma
ms.openlocfilehash: 0605780651e1a3c54ae53d13a3f99e1124fa76db
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100585003"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Autoskálázás a vendég metrikák használatával egy Linux-méretezési csoport sablonjában

Az Azure-ban két, a virtuális gépek és a méretezési csoportok által gyűjtött mérőszámok találhatók: a gazdagép metrikái és a vendég metrikái. Ha a normál processzor-, lemez-és hálózati metrikákat szeretné használni, akkor a gazdagép metrikái jól illeszkednek. Ha azonban nagyobb számú mérőszámra van szüksége, akkor a vendég metrikákat kell keresnie.

A gazdagép metrikái nem igényelnek további telepítést, mivel azokat a gazda virtuális gép gyűjti, míg a vendég metrikák megkövetelik a [Windows Azure Diagnostics bővítmény](../virtual-machines/extensions/diagnostics-template.md) vagy a [Linux Azure Diagnostics bővítmény](../virtual-machines/extensions/diagnostics-linux.md) telepítését a vendég virtuális gépen. Az egyik gyakori oka, hogy a vendég metrikák használata a gazdagép metrikái helyett az, hogy a vendég mérőszámok nagyobb választékot biztosítanak a metrikák számára, mint a gazdagép metrikái. Az egyik ilyen példa a memória-felhasználás mérőszámai, amelyek csak a vendég metrikák használatával érhetők el. A támogatott gazdagép-metrikák [itt](../azure-monitor/essentials/metrics-supported.md)vannak felsorolva, és gyakran használt vendég metrikák szerepelnek [itt](../azure-monitor/autoscale/autoscale-common-metrics.md). Ez a cikk bemutatja, hogyan módosíthatja az [alapszintű, életképes méretezési csoport sablonját](virtual-machine-scale-sets-mvss-start.md) az autoskálázási szabályok használatára a Linux-méretezési csoportok vendég metrikái alapján.

## <a name="change-the-template-definition"></a>A sablon definíciójának módosítása

Egy [korábbi cikkben](virtual-machine-scale-sets-mvss-start.md) egy alapszintű méretezési csoport sablont hoztunk létre. Most ezt a korábbi sablont fogjuk használni, és úgy módosítjuk, hogy olyan sablont hozzon létre, amely a vendég metrika-alapú autoscale használatával üzembe helyez egy Linux-méretezési készletet.

Először adja hozzá a és a paramétereit `storageAccountName` `storageAccountSasToken` . A diagnosztikai ügynök metrikus adatokat tárol ebben a Storage-fiókban található [táblában](../cosmos-db/tutorial-develop-table-dotnet.md) . A Linux diagnosztikai ügynök 3,0-es verziójától kezdve a Storage-hozzáférési kulcs használata már nem támogatott. Ehelyett használjon sas- [tokent](../storage/common/storage-sas-overview.md).

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "storageAccountName": {
+      "type": "string"
+    },
+    "storageAccountSasToken": {
+      "type": "securestring"
     }
   },
```

Ezután módosítsa a méretezési csoportját, `extensionProfile` hogy tartalmazza a diagnosztikai bővítményt. Ebben a konfigurációban adja meg annak a méretezési csoportnak az erőforrás-AZONOSÍTÓját, amely a metrikák gyűjtését, valamint a metrikák tárolásához használható Storage-fiókot és SAS-jogkivonatot használja. Itt adhatja meg, hogy a metrikák milyen gyakran legyenek összesítve (ebben az esetben percenként), és mely mérőszámokat kell követni (ebben az esetben a felhasznált memória százalékaránya). További információ erről a konfigurációról és a felhasznált memória százalékos arányáról: [ebben a dokumentációban](../virtual-machines/extensions/diagnostics-linux.md).

```diff
                 }
               }
             ]
+          },
+          "extensionProfile": {
+            "extensions": [
+              {
+                "name": "LinuxDiagnosticExtension",
+                "properties": {
+                  "publisher": "Microsoft.Azure.Diagnostics",
+                  "type": "LinuxDiagnostic",
+                  "typeHandlerVersion": "3.0",
+                  "settings": {
+                    "StorageAccount": "[parameters('storageAccountName')]",
+                    "ladCfg": {
+                      "diagnosticMonitorConfiguration": {
+                        "performanceCounters": {
+                          "sinks": "WADMetricJsonBlob",
+                          "performanceCounterConfiguration": [
+                            {
+                              "unit": "percent",
+                              "type": "builtin",
+                              "class": "memory",
+                              "counter": "percentUsedMemory",
+                              "counterSpecifier": "/builtin/memory/percentUsedMemory",
+                              "condition": "IsAggregate=TRUE"
+                            }
+                          ]
+                        },
+                        "metrics": {
+                          "metricAggregation": [
+                            {
+                              "scheduledTransferPeriod": "PT1M"
+                            }
+                          ],
+                          "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]"
+                        }
+                      }
+                    }
+                  },
+                  "protectedSettings": {
+                    "storageAccountName": "[parameters('storageAccountName')]",
+                    "storageAccountSasToken": "[parameters('storageAccountSasToken')]",
+                    "sinksConfig": {
+                      "sink": [
+                        {
+                          "name": "WADMetricJsonBlob",
+                          "type": "JsonBlob"
+                        }
+                      ]
+                    }
+                  }
+                }
+              }
+            ]
           }
         }
       }
```

Végül adjon hozzá egy `autoscaleSettings` erőforrást az autoscale ezen mérőszámok alapján történő konfigurálásához. Az erőforráshoz tartozik egy `dependsOn` záradék, amely a méretezési csoportra hivatkozik, hogy a méretezési csoport létezik, mielőtt megkísérli az autoskálázást. Ha az automatikus skálázáshoz egy másik metrikát választ, a-t a `counterSpecifier` diagnosztikai bővítmény konfigurációjában, az automatikus `metricName` skálázási konfigurációban használhatja. Az automatikus skálázási konfigurációval kapcsolatos további információkért tekintse meg az automatikus [skálázás ajánlott eljárásait](../azure-monitor/autoscale/autoscale-best-practices.md) és a [Azure monitor REST API](/rest/api/monitor/autoscalesettings)dokumentációját.

```diff
+    },
+    {
+      "type": "Microsoft.Insights/autoscaleSettings",
+      "apiVersion": "2015-04-01",
+      "name": "guestMetricsAutoscale",
+      "location": "[resourceGroup().location]",
+      "dependsOn": [
+        "Microsoft.Compute/virtualMachineScaleSets/myScaleSet"
+      ],
+      "properties": {
+        "name": "guestMetricsAutoscale",
+        "targetResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+        "enabled": true,
+        "profiles": [
+          {
+            "name": "Profile1",
+            "capacity": {
+              "minimum": "1",
+              "maximum": "10",
+              "default": "3"
+            },
+            "rules": [
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "GreaterThan",
+                  "threshold": 60
+                },
+                "scaleAction": {
+                  "direction": "Increase",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              },
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "LessThan",
+                  "threshold": 30
+                },
+                "scaleAction": {
+                  "direction": "Decrease",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              }
+            ]
+          }
+        ]
+      }
     }
   ]
 }
```





## <a name="next-steps"></a>Következő lépések

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
