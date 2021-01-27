---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/25/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 61f42e7f4dfbcfed4b6fc41d737059916e898654
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2021
ms.locfileid: "98807380"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Redis tartozó Azure cache-nek egy virtuális hálózaton belül kell lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d092e0a-7acd-40d2-a975-dca21cae48c4) |Az Azure Virtual Network (VNet) üzembe helyezése fokozott biztonságot és elkülönítést biztosít az Azure cache Redis, valamint az alhálózatok, hozzáférés-vezérlési házirendek és egyéb funkciók számára a hozzáférés további korlátozásához. Ha egy Azure cache for Redis-példány VNet van konfigurálva, nem nyilvánosan címezhető, és csak a VNet lévő virtuális gépekről és alkalmazásokról érhető el. |Naplózás, megtagadás, letiltva |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_CacheInVnet_Audit.json) |
|[Csak a Redis-hez készült Azure cache biztonságos kapcsolatainak engedélyezése szükséges](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Csak az SSL-kapcsolaton keresztüli kapcsolatok engedélyezése az Azure cache-Redis. A biztonságos kapcsolatok használata biztosítja a kiszolgáló és a szolgáltatás közötti hitelesítést, és védelmet biztosít a hálózati rétegbeli támadásoktól, például az embertől a középponttól, a lehallgatás és a munkamenet-eltérítéstől. |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
