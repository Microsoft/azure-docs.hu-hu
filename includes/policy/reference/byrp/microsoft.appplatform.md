---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/17/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 2b46aa4c782020c8b9ef3d2534d120b41b88018c
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2020
ms.locfileid: "94736130"
---
|Név<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Spring Cloud-példányok naplózása, ahol az elosztott nyomkövetés nincs engedélyezve](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f2d8593-4667-4932-acca-6a9f187af109) |Az Azure Spring Cloud elosztott nyomkövetési eszközei lehetővé teszik az alkalmazásokban található szolgáltatások közötti összetett összekapcsolások hibakeresését és figyelését. Az elosztott nyomkövetési eszközöket engedélyezni és kifogástalan állapotban kell lennie. |Naplózás, letiltva |[1.0.0 – előzetes verzió](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_DistributedTracing_Audit.json) |
|[Az Azure Spring Cloud-nak hálózati befecskendezést kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf35e2a4-ef96-44e7-a9ae-853dd97032c4) |Az Azure Spring Cloud instances szolgáltatásnak a következő célokra kell használnia a virtuális hálózati befecskendezést: 1. Az Azure Spring Cloud elkülönítése az internetről. 2. Az Azure Spring Cloud lehetővé teszi, hogy a helyszíni adatközpontokban vagy más virtuális hálózatokban lévő Azure-szolgáltatásban kommunikáljon a rendszerekkel. 3. Az Azure Spring Cloud bejövő és kimenő hálózati kommunikációjának szabályozása az ügyfelek számára. |Naplózás, letiltva, megtagadás |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_VNETEnabled_Audit.json) |
