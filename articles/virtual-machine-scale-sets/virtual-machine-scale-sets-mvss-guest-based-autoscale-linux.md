---
title: Az Azure autoscale használata vendég metrikákkal egy Linux-méretezési csoport sablonjában
description: Megtudhatja, hogyan méretezhető a vendég mérőszámok használata egy linuxos virtuálisgép-méretezési csoport sablonjában
author: mayanknayar
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: manayar
ms.openlocfilehash: 88f839b281e4d345b012a7e6acff3770dc536045
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76271952"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Autoskálázás a vendég metrikák használatával egy Linux-méretezési csoport sablonjában

Az Azure-ban két, a virtuális gépek és a méretezési csoportok által gyűjtött mérőszámok találhatók: a gazdagép metrikái és a vendég metrikái. Ha a normál processzor-, lemez-és hálózati metrikákat szeretné használni, akkor a gazdagép metrikái jól illeszkednek. Ha azonban nagyobb számú mérőszámra van szüksége, akkor a vendég metrikákat kell keresnie.

A gazdagép metrikái nem igényelnek további telepítést, mivel azokat a gazda virtuális gép gyűjti, míg a vendég metrikák megkövetelik a [Windows Azure Diagnostics bővítmény](../virtual-machines/windows/extensions-diagnostics-template.md) vagy a [Linux Azure Diagnostics bővítmény](../virtual-machines/linux/diagnostic-extension.md) telepítését a vendég virtuális gépen. Az egyik gyakori oka, hogy a vendég metrikák használata a gazdagép metrikái helyett az, hogy a vendég mérőszámok nagyobb választékot biztosítanak a metrikák számára, mint a gazdagép metrikái. Az egyik ilyen példa a memória-felhasználás mérőszámai, amelyek csak a vendég metrikák használatával érhetők el. A támogatott gazdagép-metrikák [itt](../azure-monitor/platform/metrics-supported.md)vannak felsorolva, és gyakran használt vendég metrikák szerepelnek [itt](../azure-monitor/platform/autoscale-common-metrics.md). Ez a cikk bemutatja, hogyan módosíthatja az [alapszintű, életképes méretezési csoport sablonját](virtual-machine-scale-sets-mvss-start.md) az autoskálázási szabályok használatára a Linux-méretezési csoportok vendég metrikái alapján.

## <a name="change-the-template-definition"></a>A sablon definíciójának módosítása

Egy [korábbi cikkben](virtual-machine-scale-sets-mvss-start.md) egy alapszintű méretezési csoport sablont hoztunk létre. Most ezt a korábbi sablont fogjuk használni, és úgy módosítjuk, hogy olyan sablont hozzon létre, amely a vendég metrika-alapú autoscale használatával üzembe helyez egy Linux-méretezési készletet.

Először adja hozzá a `storageAccountName` és `storageAccountSasToken`paramétereit. A diagnosztikai ügynök metrikus adatokat tárol ebben a Storage-fiókban található [táblában](../cosmos-db/table-storage-how-to-use-dotnet.md) . A Linux diagnosztikai ügynök 3,0-es verziójától kezdve a Storage-hozzáférési kulcs használata már nem támogatott. Ehelyett használjon sas- [tokent](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

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

Ezután módosítsa a méretezési csoport `extensionProfile`, hogy tartalmazza a diagnosztikai bővítményt. Ebben a konfigurációban adja meg annak a méretezési csoportnak az erőforrás-AZONOSÍTÓját, amely a metrikák gyűjtését, valamint a metrikák tárolásához használható Storage-fiókot és SAS-jogkivonatot használja. Itt adhatja meg, hogy a metrikák milyen gyakran legyenek összesítve (ebben az esetben percenként), és mely mérőszámokat kell követni (ebben az esetben a felhasznált memória százalékaránya). További információ erről a konfigurációról és a felhasznált memória százalékos arányáról: [ebben a dokumentációban](../virtual-machines/linux/diagnostic-extension.md).

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

Végül vegyen fel egy `autoscaleSettings`-erőforrást az ezen mérőszámok alapján történő autoskálázás konfigurálásához. Az erőforráshoz tartozik egy `dependsOn` záradék, amely a méretezési csoportra hivatkozik, hogy a méretezési csoport létezik, mielőtt megkísérli az autoskálázást. Ha az automatikus skálázáshoz egy másik metrikát választ, a `counterSpecifier` a diagnosztikai bővítmény konfigurációjában, az automatikus skálázási konfigurációban használt `metricName`. Az automatikus skálázási konfigurációval kapcsolatos további információkért tekintse meg az automatikus [skálázás ajánlott eljárásait](../azure-monitor/platform/autoscale-best-practices.md) és a [Azure monitor REST API](/rest/api/monitor/autoscalesettings)dokumentációját.

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
