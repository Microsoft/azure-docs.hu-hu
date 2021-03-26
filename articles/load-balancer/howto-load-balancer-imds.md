---
title: Terheléselosztó metaadatainak beolvasása az Azure Instance Metadata Service (IMDS) használatával
titleSuffix: Azure Load Balancer
description: Ismerkedjen meg a Load Balancer metaadatainak az Azure Instance Metadata Service használatával történő beolvasásával.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 02/12/2021
ms.author: allensu
ms.openlocfilehash: 95d0e1ceb9e05ce58f388c3f88dc98b2cf6a0cc5
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105559587"
---
# <a name="retrieve-load-balancer-metadata-using-the-azure-instance-metadata-service-imds"></a>Terheléselosztó metaadatainak beolvasása az Azure Instance Metadata Service (IMDS) használatával

## <a name="prerequisites"></a>Előfeltételek

* A kérelméhez használja a [legújabb API-verziót](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#supported-api-versions) .

## <a name="sample-request-and-response"></a>Példa a kérelemre és a válaszra
> [!IMPORTANT]
> Ez a példa kihagyja a proxykat. A IMDS lekérdezése során meg **kell** kerülnie a proxykat. További információ: [proxys](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#proxies).
### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254:80/metadata/loadbalancer?api-version=2020-10-01" | ConvertTo-Json
```
> [!NOTE]
> A-No proxy paraméter a PowerShell 6,0-ben lett bevezetve. Ha a PowerShell egy régebbi verzióját használja, távolítsa el a-proxyt a kérelem törzsében, és győződjön meg arról, hogy nem használ proxyt a IMDS-adatok beolvasása közben. További információ [itt](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#proxies).
> 
### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H "Metadata:true" --noproxy "*" "http://169.254.169.254:80/metadata/loadbalancer?api-version=2020-10-01"
```

---
### <a name="sample-response"></a>Mintaválasz

```json
{
   "loadbalancer": {
    "publicIpAddresses":[
      {
         "frontendIpAddress":"51.0.0.1",
         "privateIpAddress":"10.1.0.4"
      }
   ],
   "inboundRules":[
      {
         "frontendIpAddress":"50.0.0.1",
         "protocol":"tcp",
         "frontendPort":80,
         "backendPort":443,
         "privateIpAddress":"10.1.0.4"
      },
      {
         "frontendIpAddress":"2603:10e1:100:2::1:1",
         "protocol":"tcp",
         "frontendPort":80,
         "backendPort":443,
         "privateIpAddress":"ace:cab:deca:deed::1"
      }
   ],
   "outboundRules":[
      {
         "frontendIpAddress":"50.0.0.1",
         "privateIpAddress":"10.1.0.4"
      },
      {
         "frotendIpAddress":"2603:10e1:100:2::1:1",
         "privateIpAddress":"ace:cab:deca:deed::1"
      }
    ]
   }
}

```

## <a name="next-steps"></a>Következő lépések
[Gyakori hibakódok és hibaelhárítási lépések](troubleshoot-load-balancer-imds.md)

További információ az [Azure instance metadata Service](../virtual-machines/windows/instance-metadata-service.md)

[Példány összes metaadatának beolvasása](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#access-azure-instance-metadata-service)

[Standard Load Balancer üzembe helyezése](quickstart-load-balancer-standard-public-portal.md)