---
title: Az Azure Spring Cloud szolgáltatási csomagjai és kvótái
description: Ismerje meg az Azure Spring Cloud szolgáltatáshoz kapcsolódó kvótákat és szolgáltatási terveket
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 496f2e812a102e85fea92a535552daaaadf5f31e
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2020
ms.locfileid: "92093429"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Kvóták és szolgáltatási csomagok az Azure Spring Cloud-hoz

**Ez a cikk a következőkre vonatkozik:** ✔️ Java ✔️ C #

Az összes Azure-szolgáltatás alapértelmezett korlátokat és kvótákat állít be az erőforrásokhoz és a szolgáltatásokhoz.   Az Azure Spring Cloud két díjszabási szintet kínál: alapszintű és standard. A jelen cikk mindkét szintjére vonatkozóan részletezjük a korlátot.

## <a name="azure-spring-cloud-service-tiers-and-limits"></a>Az Azure Spring Cloud Service szintjei és korlátai

| Erőforrás | Alapszintű | Standard
------- | ------- | -------
vCPU | 1/szolgáltatási példány | 4/szolgáltatási példány
Memória | 2 GB/szolgáltatási példány | 8 GB/szolgáltatási példány
Azure Spring Cloud Service instances régiónként/előfizetés | 10 | 10
Alkalmazás-példányok teljes száma Azure Spring Cloud Service-példányon | 25 | 500
Tartós kötetek | 1 GB/app x 10 alkalmazás | 50 GB/app x 10 alkalmazás

## <a name="next-steps"></a>Következő lépések

Néhány alapértelmezett korlát növelhető. Ha a telepítőnek növekedésre van szüksége, [hozzon létre egy támogatási kérést](../azure-portal/supportability/how-to-create-azure-support-request.md).