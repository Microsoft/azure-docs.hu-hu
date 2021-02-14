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
ms.openlocfilehash: 35e32627eaf26eb6f73ca382f119eab61ebd8711
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417831"
---
# <a name="retrieve-load-balancer-metadata-using-the-azure-instance-metadata-service-imds"></a>Terheléselosztó metaadatainak beolvasása az Azure Instance Metadata Service (IMDS) használatával

## <a name="prerequisites"></a>Előfeltételek

* A kérelméhez használja a [legújabb API-verziót](/virtual-machines/windows/instance-metadata-service?tabs=windows#supported-api-versions) .

## <a name="sample-request-and-response"></a>Példa a kérelemre és a válaszra
> [!IMPORTANT]
> Ez a példa kihagyja a proxykat. A IMDS lekérdezése során meg **kell** kerülnie a proxykat. További információ: [proxys](/virtual-machines/windows/instance-metadata-service?tabs=windows#proxies).
### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254:80/metadata/loadbalancer?api-version=2020-10-01" | ConvertTo-Json
```

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

További információ az [Azure instance metadata Service](/virtual-machines/windows/instance-metadata-service)

[Példány összes metaadatának beolvasása](/virtual-machines/windows/instance-metadata-service?tabs=windows#access-azure-instance-metadata-service)

[Standard Load Balancer üzembe helyezése](quickstart-load-balancer-standard-public-portal.md)

