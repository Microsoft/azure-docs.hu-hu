---
title: Metrikák beolvasása a REST API
titleSuffix: Azure Load Balancer
description: Ez a cikk az Azure REST API-k használatának első lépéseit ismerteti a Azure Load Balancer állapotának és használati metrikáinak összegyűjtéséhez.
services: sql-database
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.custom: REST, seodec18
ms.topic: how-to
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: 3b5aedb20bc7a8d2aa6f3aa3d8691a71af4cd3a2
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/16/2020
ms.locfileid: "84808370"
---
# <a name="get-load-balancer-usage-metrics-using-the-rest-api"></a>Load Balancer használati metrikák beszerzése a REST API használatával

A [standard Load Balancer](/azure/load-balancer/load-balancer-standard-overview) által feldolgozott bájtok számának összegyűjtése az [Azure REST API](/rest/api/azure/)használatával.

A REST API dokumentációja és a további minták a [Azure monitor Rest-referenciában](/rest/api/monitor)érhetők el. 

## <a name="build-the-request"></a>A kérelem felépítése

A következő GET kérelem használatával gyűjtheti össze a [ByteCount metrikáját](/azure/load-balancer/load-balancer-standard-diagnostics#multi-dimensional-metrics) egy standard Load Balancer. 

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=ByteCount&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>Kérésfejlécek

A következő fejlécek megadása kötelező: 

|Kérelem fejléce|Leírás|  
|--------------------|-----------------|  
|*Content-Type* (Tartalomtípus):|Kötelező. Állítsa `application/json` értékre.|  
|*Authorization* (Engedélyezés):|Kötelező. Állítsa egy érvényes `Bearer` [hozzáférési jogkivonatra](/rest/api/azure/#authorization-code-grant-interactive-clients). |  

### <a name="uri-parameters"></a>URI-paraméterek

| Name | Leírás |
| :--- | :---------- |
| subscriptionId | Az Azure-előfizetést azonosító előfizetés azonosítója. Ha több előfizetéssel rendelkezik, tekintse meg [a több előfizetés használata](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)című témakört. |
| resourceGroupName | Az erőforrást tartalmazó erőforráscsoport neve. Ezt az értéket a Azure Resource Manager API-ból, a CLI-ből vagy a portálról szerezheti be. |
| loadBalancerName | A Azure Load Balancer neve. |
| metrikák nevei | Az érvényes [Load Balancer metrikák](/azure/load-balancer/load-balancer-standard-diagnostics)vesszővel tagolt listája. |
| api-verzió | A kérelemhez használandó API-verzió.<br /><br /> Ez a dokumentum `2018-01-01` a fenti URL-cím részét képező API-verziót tartalmazza.  |
| időtartomány | A lekérdezés TimeSpan. Ez egy karakterlánc, amely a következő formátumú `startDateTime_ISO/endDateTime_ISO` . Ez a választható paraméter úgy van beállítva, hogy a példában egy nap értékű adatot adja vissza. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>A kérés törzse

Ehhez a művelethez nem szükséges a kérelem törzse.

## <a name="handle-the-response"></a>A válasz kezelése

A rendszer a 200-as állapotkódot adja vissza, ha a metrikai értékek listáját sikeresen visszaadja. A hibakódok teljes listája megtalálható a [dokumentációban](/rest/api/monitor/metrics/list#errorresponse).

## <a name="example-response"></a>Példaválasz 

```json
{
    "cost": 0,
    "timespan": "2018-06-05T03:00:00Z/2018-06-07T03:00:00Z",
    "interval": "PT1M",
    "value": [
        {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/Microsoft.Insights/metrics/ByteCount",
            "type": "Microsoft.Insights/metrics",
            "name": {
                "value": "ByteCount",
                "localizedValue": "Byte Count"
            },
            "unit": "Count",
            "timeseries": [
                {
                    "metadatavalues": [],
                    "data": [
                        {
                            "timeStamp": "2018-06-06T17:24:00Z",
                            "total": 1067921034.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:25:00Z",
                            "total": 0.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:26:00Z",
                            "total": 3781344.0
                        },
                    ]
                }
            ]
        }
    ],
    "namespace": "Microsoft.Network/loadBalancers",
    "resourceregion": "eastus"
}
```
