---
title: Speciális autoskálázás az Azure Virtual Machines
description: A Resource Managerrel és a virtuálisgép-méretezési csoportokkal több szabályt és profilt használ, amelyek e-maileket küldenek, és felhívhatják a webhook URL-címeit
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 06/25/2020
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 26cfdf05847b01d28a8574483acc89cfeced0ffa
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101717740"
---
# <a name="advanced-autoscale-configuration-using-resource-manager-templates-for-vm-scale-sets"></a>Speciális automatikus méretezési konfiguráció a Resource Manager-sablonokkal VM Scale Sets
A Virtual Machine Scale Sets méretezési és kibővíthető teljesítmény-metrikai küszöbértékek, ismétlődő ütemterv vagy egy adott dátum alapján. A méretezési műveletekhez e-mail-és webhook-értesítéseket is konfigurálhat. Ez a forgatókönyv egy virtuálisgép-méretezési csoport Resource Manager-sablonnal történő konfigurálásának példáját mutatja be.

> [!NOTE]
> Habár ez a bemutató ismerteti a VM Scale Sets lépéseit, ugyanazok az adatok vonatkoznak az automatikus skálázási [Cloud Servicesra](https://azure.microsoft.com/services/cloud-services/), a [app Service-web Appsra](https://azure.microsoft.com/services/app-service/web/)és a [API Management szolgáltatásokra](../../api-management/api-management-key-concepts.md) a virtuálisgép-méretezési csoport egyszerű teljesítmény mérőszáma, például a CPU alapján, a [Linux](../../virtual-machine-scale-sets/tutorial-autoscale-cli.md) -és [Windows](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md) -dokumentumokban.
>
>

## <a name="walkthrough"></a>Útmutatás
Ebben az útmutatóban a méretezési csoportokra vonatkozóan a [Azure erőforrás-kezelő](https://resources.azure.com/) használatával konfigurálhatja és frissítheti az autoskálázási beállítást. A Azure Erőforrás-kezelő használatával egyszerűen kezelheti az Azure-erőforrásokat Resource Manager-sablonokkal. Ha még nem Azure Erőforrás-kezelő eszközt, olvassa el [ezt a bevezetést](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/).

1. Helyezzen üzembe egy új méretezési készletet egy alapszintű autoskálázási beállítással. Ez a cikk az Azure gyors üzembe helyezési galériájának egyikét használja, amely egy alapszintű autoskálázási sablonnal rendelkező Windows-méretezési csoporttal rendelkezik. A Linux-méretezési csoportok ugyanúgy működnek.
2. A méretezési csoport létrehozása után navigáljon a méretezési csoport erőforrásához Azure Erőforrás-kezelő. A következőt látja a Microsoft. bepillantások csomópont alatt.

    ![Azure Explorer](media/autoscale-virtual-machine-scale-sets/azure_explorer_navigate.png)

    A sablon végrehajtása létrehozta a **"autoscalewad"** nevű alapértelmezett autoskálázási beállítást. A jobb oldalon megtekintheti ennek az autoskálázási beállításnak a teljes definícióját. Ebben az esetben az alapértelmezett autoskálázási beállítás egy CPU%-alapú kibővített és méretezési szabályt tartalmaz.  

3. Mostantól további profilokat és szabályokat adhat hozzá az ütemterv vagy a speciális követelmények alapján. Létrehozunk egy három profillal rendelkező autoskálázási beállítást. Az autoscale-profilok és-szabályok megértéséhez tekintse át az [ajánlott eljárások az autoscale](autoscale-best-practices.md)-ben című témakört.  

    | Profilok & szabályok | Leírás |
    |--- | --- |
    | **Profil** |**Teljesítmény/metrika-alapú** |
    | Szabály |Service Bus üzenetsor-üzenetek száma > x |
    | Szabály |Service Bus üzenetsor-üzenetek száma < y |
    | Szabály |CPU% > n |
    | Szabály |CPU% < p |
    | **Profil** |**Hétköznap reggel óra (nincs szabály)** |
    | **Profil** |**Termék indításának napja (nincs szabály)** |

4. Íme egy feltételezett méretezési forgatókönyv, amelyet ehhez az útmutatóhoz használunk.

   * **Betöltés** – a méretezési csoporton üzemeltetett alkalmazás terhelése alapján szeretném felskálázást végezni. *
   * **Üzenet-várólista mérete** – a beérkező üzenetekhez Service Bus várólistát használok az alkalmazáshoz. A várólista üzeneteinek számát és a CPU-t használja, és az alapértelmezett profilt úgy konfigurálja, hogy kiváltsa a méretezési műveletet, ha az üzenetek száma vagy a CPU eléri a küszöbértéket.\*
   * **Hét és nap időpontja** – azt szeretném, hogy a nap egy heti ismétlődési ideje "hétköznap reggel órája". A korábbi adatmennyiségek alapján biztos lehet benne, hogy az alkalmazás terhelésének kezelése ebben az időszakban jobb.\*
   * **Különleges dátumok** – Felvettem egy "termék elindítása nap" profilt. Előre tervezem a megadott dátumokat, így az alkalmazásom készen áll a marketinggel kapcsolatos bejelentések betöltésére, és amikor új terméket teszünk az alkalmazásba.\*
   * *Az utolsó két profil más teljesítménymutató-alapú szabályokkal is rendelkezhet. Ebben az esetben úgy döntöttem, hogy nem az egyikre, hanem az alapértelmezett teljesítmény-metrikai szabályokra támaszkodik. A szabályok nem kötelezőek az ismétlődő és a dátum-alapú profilokhoz.*

     Az automatikus méretezési motor a profilok és a szabályok rangsorolását is rögzíti az automatikus [skálázás ajánlott eljárásai](autoscale-best-practices.md) című cikkben.
     Az autoscale általános mérőszámait az [autoscale általános mérőszámai](autoscale-common-metrics.md) című témakörben tekintheti meg.

5. Győződjön meg arról, hogy az **olvasási/írási** módban van erőforrás-kezelő

    ![Autoscalewad, alapértelmezett autoskálázási beállítás](media/autoscale-virtual-machine-scale-sets/autoscalewad.png)

6. Kattintson a Szerkesztés gombra. **Cserélje le** a "profilok" elemet az automatikus skálázási beállításban a következő konfigurációval:

    ![Képernyőfelvétel a profilok elemet mutatja.](media/autoscale-virtual-machine-scale-sets/profiles.png)

    ```
    {
            "name": "Perf_Based_Scale",
            "capacity": {
              "minimum": "2",
              "maximum": "12",
              "default": "2"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "MessageCount",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 10
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "MessageCount",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 3
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "Percentage CPU",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT30M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 85
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "Percentage CPU",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT30M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 60
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              }
            ]
          },
          {
            "name": "Weekday_Morning_Hours_Scale",
            "capacity": {
              "minimum": "4",
              "maximum": "12",
              "default": "4"
            },
            "rules": [],
            "recurrence": {
              "frequency": "Week",
              "schedule": {
                "timeZone": "Pacific Standard Time",
                "days": [
                  "Monday",
                  "Tuesday",
                  "Wednesday",
                  "Thursday",
                  "Friday"
                ],
                "hours": [
                  6
                ],
                "minutes": [
                  0
                ]
              }
            }
          },
          {
            "name": "Product_Launch_Day",
            "capacity": {
              "minimum": "6",
              "maximum": "20",
              "default": "6"
            },
            "rules": [],
            "fixedDate": {
              "timeZone": "Pacific Standard Time",
              "start": "2016-06-20T00:06:00Z",
              "end": "2016-06-21T23:59:00Z"
            }
          }
    ```
    A támogatott mezőkhöz és azok értékeihez lásd: az [autoscale REST API dokumentációja](/rest/api/monitor/autoscalesettings). Az autoskálázási beállítás most már tartalmazza a korábban ismertetett három profilt.

7. Végül tekintse meg az autoskálázás **értesítési** szakaszt. Az autoscale Notifications lehetővé teszi, hogy három dolgot végezzen el a Kibővítés vagy a működés sikeres elindításához.
   - Az előfizetés rendszergazdája és a társ-rendszergazdák értesítése
   - Felhasználók e-mail-címe
   - Webhook-hívás indítása. Ez a webhook az automatikus skálázási feltétellel és a méretezési csoport erőforrásaival kapcsolatos metaadatokat küld. Ha többet szeretne megtudni az autoscale webhook hasznos adatairól, tekintse meg a [webhook & e-mail-értesítések konfigurálása az autoskálázáshoz](autoscale-webhook-email.md)című témakört.

   Adja hozzá a következőt az autoskálázási beállításhoz, amely felülírja az **értesítési** elemet, amelynek értéke null

   ```
   "notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": true,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]

   ```

   Az autoskálázási beállítás frissítéséhez nyomja meg erőforrás-kezelő a **put** gombot.

Frissített egy, a virtuálisgép-méretezési csoporton egy olyan méretezési beállítást, amely több skálázási profilt és méretezési értesítéseket is tartalmaz.

## <a name="next-steps"></a>Következő lépések
Ezekkel a hivatkozásokkal többet tudhat meg az automatikus skálázásról.

[Az Virtual Machine Scale Sets](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

[Gyakori metrikák az autoskálázáshoz](autoscale-common-metrics.md)

[Ajánlott eljárások az Azure-ra való autoskálázáshoz](autoscale-best-practices.md)

[Az autoskálázás kezelése a PowerShell használatával](../powershell-samples.md#create-and-manage-autoscale-settings)

[Az autoskálázás kezelése a parancssori felület használatával](../cli-samples.md#autoscale)

[Webhook & e-mail-értesítések konfigurálása az autoskálázáshoz](autoscale-webhook-email.md)

[Microsoft. bepillantások/autoscalesettings-](/azure/templates/microsoft.insights/autoscalesettings) sablonok referenciája