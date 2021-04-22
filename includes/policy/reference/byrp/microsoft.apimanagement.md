---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 6c54ddf232d16b0555ec8e1f5fee57e1f597488f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866244"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API Management szolgáltatásnak olyan termékváltozatot kell használnia, amely támogatja a virtuális hálózatokat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F73ef9241-5d81-4cd4-b483-8443d1730fe5) |A szolgáltatás virtuális API Management támogatott SKUS-jának használata fejlett hálózati és biztonsági funkciókat biztosít, így nagyobb irányítást biztosít API Management a hálózati biztonsági konfiguráció felett. További információ: [https://aka.ms/apimvnet](https://aka.ms/apimvnet) . |Naplózás, Megtagadás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_AllowedVNETSkus_AuditDeny.json) |
|[API Management szolgáltatásoknak virtuális hálózatot kell használniuk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef619a2c-cc4d-4d03-b2ba-8c94a834d85b) |Az Azure Virtual Network üzemelő példánya fokozott biztonságot és elkülönítést biztosít, és lehetővé teszi, hogy API Management-szolgáltatását egy nem internetre irányítható hálózatba helyezze, amelyhez a hozzáférést Ön vezéreli. Ezek a hálózatok ezután különböző VPN-technológiákkal csatlakoztathatóak a helyszíni hálózatokhoz, ami lehetővé teszi a háttérszolgáltatásokhoz való hozzáférést a hálózaton és/vagy a helyszínen. A fejlesztői portál és az API-átjáró úgy konfigurálható, hogy elérhető legyen az internetről vagy csak a virtuális hálózaton belül. |Naplózás, Letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_VNETEnabled_Audit.json) |
