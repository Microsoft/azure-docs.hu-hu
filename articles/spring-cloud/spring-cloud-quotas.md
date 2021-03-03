---
title: Az Azure Spring Cloud szolgáltatási csomagjai és kvótái
description: Ismerje meg az Azure Spring Cloud szolgáltatáshoz kapcsolódó kvótákat és szolgáltatási terveket
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: b02ccb3acb4546e08e7d58159ab9d85bca2d0eed
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101711875"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Kvóták és szolgáltatási csomagok az Azure Spring Cloud-hoz

**Ez a cikk a következőkre vonatkozik:** ✔️ Java ✔️ C #

Az összes Azure-szolgáltatás alapértelmezett korlátokat és kvótákat állít be az erőforrásokhoz és a szolgáltatásokhoz.   Az Azure Spring Cloud két díjszabási szintet kínál: alapszintű és standard. A jelen cikk mindkét szintjére vonatkozóan részletezjük a korlátot.

## <a name="azure-spring-cloud-service-tiers-and-limits"></a>Az Azure Spring Cloud Service szintjei és korlátai

| Erőforrás | Hatókör | Alapszintű | Standard
------- | ------- | -------
vCPU | alkalmazás-példány | 1 | 4
Memória | alkalmazás-példány | 2 GB | 8 GB
Azure Spring Cloud Service-példányok | régiónként/előfizetés | 10 | 10
Alkalmazás összes példánya | /Azure Spring Cloud Service-példány | 25 | 500
Egyéni tartományok | /Azure Spring Cloud Service-példány | 0 | 25 
Állandó kötetek | /Azure Spring Cloud Service-példány | 1 GB/app x 10 alkalmazás | 50 GB/app x 10 alkalmazás

> [!TIP]
> A szolgáltatási példányok teljes alkalmazási példányaira vonatkozó korlátozások az alkalmazások és a központi telepítések esetében minden állapotban érvényesek, beleértve a leállított állapotot is. Törölje a nem használt alkalmazásokat vagy központi telepítéseket.

## <a name="next-steps"></a>Következő lépések

Néhány alapértelmezett korlát növelhető. Ha a telepítőnek növekedésre van szüksége, [hozzon létre egy támogatási kérést](../azure-portal/supportability/how-to-create-azure-support-request.md).
