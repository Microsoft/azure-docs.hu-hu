---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 19c5d2ef3845d4939aa28d07bee4506422916262
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107499315"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Naplózás Azure Spring Cloud olyan példányok esetén, amelyeken nincs engedélyezve az elosztott nyomkövetés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f2d8593-4667-4932-acca-6a9f187af109) |Az elosztott nyomkövetési eszközök Azure Spring Cloud lehetővé teszik az alkalmazások mikroszolgáltatásai közötti összetett kapcsolatok hibakeresését és monitorozását. Az elosztott nyomkövetési eszközöket engedélyezni kell, és kifogástalan állapotban kell lennie. |Naplózás, Letiltva |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_DistributedTracing_Audit.json) |
|[Azure Spring Cloud használjon hálózati injektálást](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf35e2a4-ef96-44e7-a9ae-853dd97032c4) |Azure Spring Cloud példánynak a következő célokra kell virtuális hálózati injektálást használnia: 1. Az Azure Spring Cloud elkülönítése az internettől. 2. Lehetővé Azure Spring Cloud, hogy a felhasználók helyszíni adatközpontokban vagy más virtuális hálózatokon lévő Azure-szolgáltatásokban lévő rendszerekkel kommunikálhatnak. 3. Az ügyfelek szabályozhatja a bejövő és kimenő hálózati kommunikációt a Azure Spring Cloud. |Naplózás, Letiltva, Megtagadás |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_VNETEnabled_Audit.json) |
