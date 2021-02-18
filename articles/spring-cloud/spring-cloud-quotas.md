---
title: Az Azure Spring Cloud szolgáltatási csomagjai és kvótái
description: Ismerje meg az Azure Spring Cloud szolgáltatáshoz kapcsolódó kvótákat és szolgáltatási terveket
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 20ebeb23fe09ba4fd70a724828afadfaa3901abd
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095661"
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
> A szolgáltatási példányok összes példánya esetében a felsorolt díjak a leállított állapotú alkalmazások/központi telepítések esetében érvényesek. Törölje a nem használt alkalmazásokat és központi telepítéseket.

## <a name="next-steps"></a>Következő lépések

Néhány alapértelmezett korlát növelhető. Ha a telepítőnek növekedésre van szüksége, [hozzon létre egy támogatási kérést](../azure-portal/supportability/how-to-create-azure-support-request.md).
