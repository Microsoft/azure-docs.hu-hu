---
title: Szolgáltatási kvóták és régiók rendelkezésre állása
description: A Azure Container Instances szolgáltatás kvótái, korlátai és régiójának rendelkezésre állása.
ms.topic: article
ms.date: 07/22/2020
ms.openlocfilehash: eaaa8e0b2d72aaea546a1bc351da40932c1deb14
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87384828"
---
# <a name="quotas-and-limits-for-azure-container-instances"></a>A Azure Container Instances kvótái és korlátai

Minden Azure-szolgáltatás tartalmaz az erőforrásokra és a funkciókra vonatkozó alapértelmezett korlátokat. Ez a cikk a Azure Container Instances alapértelmezett kvótáit és korlátait részletezi.

A Azure Container Instances számítási, memória-és tárolási erőforrásainak rendelkezésre állása régiónként és operációs rendszertől függően változik. Részletekért lásd: [az erőforrás rendelkezésre állása Azure Container instances](container-instances-region-availability.md)számára.

Az előfizetéshez tartozó régió aktuális kvóta-használatának áttekintéséhez használja a [használati](/rest/api/container-instances/location/listusage) API-t.

## <a name="service-quotas-and-limits"></a>Szolgáltatási kvóták és korlátok

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

## <a name="next-steps"></a>Következő lépések

Egyes alapértelmezett korlátok és kvóták növelhetők. Az ezt támogató erőforrások növelésének kéréséhez nyújtson be egy [Azure-támogatási kérést][azure-support] (a **Probléma típusa** mezőben válassza a „Kvóta” lehetőséget).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
