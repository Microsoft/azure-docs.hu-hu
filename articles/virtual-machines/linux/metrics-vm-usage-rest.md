---
title: Azure-beli virtuálisgép-használati adatok beszerzése a REST API használatával
description: Használja az Azure REST API-kat a virtuális gépek kihasználtsági metrikáinak összegyűjtéséhez.
author: rloutlaw
ms.service: virtual-machines
ms.subservice: monitoring
ms.custom: REST
ms.topic: article
ms.date: 06/13/2018
ms.author: routlaw
ms.openlocfilehash: 1aa108d7f903ed791c534b2b88550eb8d022ef64
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86502078"
---
# <a name="get-virtual-machine-usage-metrics-using-the-rest-api"></a>Virtuális gépek használati metrikáinak beolvasása a REST API használatával

Ez a példa bemutatja, hogyan kérhető le egy linuxos virtuális gép CPU-használata az [Azure REST API](/rest/api/azure/)használatával.

A REST API dokumentációja és a további minták a [Azure monitor Rest-referenciában](/rest/api/monitor)érhetők el. 

## <a name="build-the-request"></a>A kérelem felépítése

Az alábbi GET kérelem használatával gyűjtheti össze a [CPU-metrika százalékos arányát](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines) egy virtuális gépről.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>Kérésfejlécek

A következő fejlécek megadása kötelező: 

|Kérelem fejléce|Leírás|  
|--------------------|-----------------|  
|*Content-Type* (Tartalomtípus):|Kötelező. Állítsa `application/json` értékre.|  
|*Authorization* (Engedélyezés):|Kötelező. Állítsa egy érvényes `Bearer` [hozzáférési jogkivonatra](/rest/api/azure/#authorization-code-grant-interactive-clients). |  

### <a name="uri-parameters"></a>URI-paraméterek

| Név | Leírás |
| :--- | :---------- |
| subscriptionId | Az Azure-előfizetést azonosító előfizetés azonosítója. Ha több előfizetéssel rendelkezik, tekintse meg [a több előfizetés használata](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)című témakört. |
| resourceGroupName | Az erőforráshoz társított Azure-erőforráscsoport neve. Ezt az értéket a Azure Resource Manager API, a CLI vagy a portál használatával szerezheti be. |
| vmname | Az Azure-beli virtuális gép neve. |
| metricnames | Az érvényes [Load Balancer metrikák](../../load-balancer/load-balancer-standard-diagnostics.md)vesszővel tagolt listája. |
| api-verzió | A kérelemhez használandó API-verzió.<br /><br /> Ez a dokumentum `2018-01-01` a fenti URL-cím részét képező API-verziót tartalmazza.  |
| időtartomány | Karakterlánc a következő formátumban, `startDateTime_ISO/endDateTime_ISO` amely meghatározza a visszaadott metrikák időtartományát. Ez a választható paraméter úgy van beállítva, hogy a példában egy nap értékű adatot adja vissza. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>A kérés törzse

Ehhez a művelethez nem szükséges a kérelem törzse.

## <a name="handle-the-response"></a>A válasz kezelése

A rendszer a 200-as állapotkódot adja vissza, ha a metrikai értékek listáját sikeresen visszaadja. A hibakódok teljes listája megtalálható a [dokumentációban](/rest/api/monitor/metrics/list#errorresponse).

## <a name="example-response"></a>Példaválasz 

```json
{
    "cost": 0,
    "timespan": "2018-06-08T23:48:10Z/2018-06-09T00:48:10Z",
    "interval": "PT1M",
    "value": [
        {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU",
            "type": "Microsoft.Insights/metrics",
            "name": {
                "value": "Percentage CPU",
                "localizedValue": "Percentage CPU"
            },
            "unit": "Percent",
            "timeseries": [
                {
                    "metadatavalues": [],
                    "data": [
                        {
                            "timeStamp": "2018-06-08T23:48:00Z",
                            "average": 0.44
                        },
                        {
                            "timeStamp": "2018-06-08T23:49:00Z",
                            "average": 0.31
                        },
                        {
                            "timeStamp": "2018-06-08T23:50:00Z",
                            "average": 0.29
                        },
                        {
                            "timeStamp": "2018-06-08T23:51:00Z",
                            "average": 0.29
                        },
                        {
                            "timeStamp": "2018-06-08T23:52:00Z",
                            "average": 0.285
                        } ]
                } ]
        } ]
}
```
