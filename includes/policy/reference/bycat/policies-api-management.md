---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 9b4727b34bba563e9f1235771fee34273264726b
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102610784"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[API Management szolgáltatásnak olyan SKU-t kell használnia, amely támogatja a virtuális hálózatokat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F73ef9241-5d81-4cd4-b483-8443d1730fe5) |A API Management támogatott SKU-ban a szolgáltatás üzembe helyezése virtuális hálózatban feloldja a speciális API Management hálózatkezelési és biztonsági funkciókat, ami nagyobb mértékben szabályozza a hálózati biztonsági konfigurációt. További információ: [https://aka.ms/apimvnet](https://aka.ms/apimvnet) . |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_AllowedVNETSkus_AuditDeny.json) |
|[API Management szolgáltatásoknak virtuális hálózatot kell használniuk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef619a2c-cc4d-4d03-b2ba-8c94a834d85b) |Az Azure Virtual Network üzembe helyezése fokozott biztonságot, elkülönítést biztosít, és lehetővé teszi, hogy a API Management szolgáltatást egy nem internetes, útválasztásra jogosult hálózatban helyezze el, amelynek hozzáférését szabályozhatja. Ezek a hálózatok ezután különböző VPN-technológiákkal csatlakozhatnak a helyszíni hálózatokhoz, ami lehetővé teszi a hálózati és/vagy helyszíni háttér-szolgáltatásokhoz való hozzáférést. A fejlesztői portál és az API-átjáró úgy konfigurálható, hogy az internetről vagy csak a virtuális hálózaton belül legyen elérhető. |Naplózás, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_VNETEnabled_Audit.json) |
