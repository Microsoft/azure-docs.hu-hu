---
title: Azure Monitor API-nyugdíjazás
description: Ismerteti a OperationalInsights erőforrás-szolgáltató API régebbi verzióinak kivonulását.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/29/2020
ms.openlocfilehash: 03e534564b4a32f112c2aaff44a187b10eb2a537
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101723112"
---
# <a name="operationalinsights-api-version-retirement"></a>OperationalInsights API-verziójának nyugdíjazása
A Microsoft legalább 12 hónappal korábban értesítést küld egy API kivonásáról, hogy zökkenőmentes legyen az áttérés egy újabb/támogatott verzióra. Megjelent egy új verzió (2020-08-01) a **OperationalInsights** erőforrás-szolgáltató API-khoz, és minden korábbi API-verziót kivonja a 2024. február 29-én.

Javasoljuk, hogy a 2020-08-01-es verzió használatának megkezdéséhez használja az új funkciók előnyeit, például a [dedikált fürtöt](./logs-dedicated-clusters.md), az [ügyfél által felügyelt kulcsokat](../logs/customer-managed-keys.md), a [privát kapcsolatot](./private-link-security.md) és az [adatexportálást](./logs-data-export.md). Emellett az új funkciók és funkciók és optimalizációk csak az aktuális API-hoz lesznek hozzáadva.

A 2024. február 29. után a Azure Monitor már nem fogja támogatni a 2020-08-01-nál korábbi API-verziókat. Ha nem szeretné frissíteni, a korábbi verziókból küldött kérelmeket a Azure Monitor szolgáltatás a 2024. február 29-én továbbra is kiszolgálja.

## <a name="migration-steps"></a>A migrálás lépései
A használt konfigurációs módszertől függően frissítenie kell az új verziót a **Rest** -kérelmekben és a **Resource Manager-sablonokban**. Az API verziójának frissítéséhez kövesse az alábbi példákat:

1. REST API kérelmek az API-verziót használják a kérelem URL-címében. Cserélje le az adott verziót a legújabb verzióra (2020-08-01) az alábbi példában látható módon.

    ```rest
    https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}?api-version=2020-08-01
    ```

2. Azure Resource Manager-sablonok az erőforrás **apiVersion** tulajdonságában az API-verziót használják. Cserélje le az adott verziót a legújabb verzióra (2020-08-01) az alábbi példában látható módon.


    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string",
                "metadata": {
                "description": "Name of the workspace."
                }
            },
            "resources": [
            {
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('workspaceName')]",
                "apiVersion": "2020-08-01",
                "location": "westus",
                "properties": {
                    "sku": {
                        "name": "pergb2018"
                    },
                    "retentionInDays": 30,
                    "features": {
                        "searchVersion": 1,
                        "legacy": 0,
                        "enableLogAccessUsingOnlyResourcePermissions": true
                    }
                }
            }
        ]
    }
    }
    ```


### <a name="more-information"></a>További információ
Ha kérdése van, választ kaphat a [technikai Közösség szakértőitől]( https://techcommunity.microsoft.com/t5/azure-monitor/bd-p/AzureMonitor). Ha támogatási csomaggal rendelkezik, és technikai segítségre van szüksége, hozzon létre egy [támogatási kérést]( https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest): 
1.  A *probléma típusa* területen válassza a **technikai** lehetőséget. 
2.  Az *Előfizetés* alatt válassza ki az előfizetését. 
3.  A *szolgáltatás* területen válassza **a saját szolgáltatások**, majd a **log Analytics** lehetőséget. 
4.  Az *Összefoglalás* területen adja meg a probléma leírását. 
5.  A *probléma típusa* területen válassza a **log Analytics munkaterület-kezelés** lehetőséget.  
6.  A *probléma altípusa* alatt válassza az **ARM-sablonok, a POWERSHELL és a CLI** lehetőséget. 

## <a name="next-steps"></a>Következő lépések

- Tekintse [meg a OperationalInsights-munkaterület API-ra mutató hivatkozást](/rest/api/loganalytics/workspaces).