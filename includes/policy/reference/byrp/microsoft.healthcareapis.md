---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 95aa94f8e1459459a941c96e0d06d13d76a38879
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2020
ms.locfileid: "94986392"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A FHIR készült Azure API-nak ügyfél által felügyelt kulcsot (CMK) kell használnia a REST-adatok titkosításához](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F051cba44-2429-45b9-9649-46cec11c7119) |A FHIR Azure API-ban tárolt adatok titkosítását az ügyfél által felügyelt kulcs segítségével szabályozhatja, ha ez szabályozási vagy megfelelőségi követelmény. Az ügyfél által felügyelt kulcsok emellett kettős titkosítást is biztosítanak, ha egy második titkosítási réteget adnak hozzá, amely a szolgáltatás által felügyelt kulcsokkal együtt az alapértelmezetten történik. |naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_EnableByok_Audit.json) |
|[A FHIR készült Azure API-nak privát hivatkozást kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ee56206-5dd1-42ab-b02d-8aae8b1634ce) |A FHIR készült Azure API-nak legalább egy jóváhagyott privát végponti kapcsolatban kell lennie. A virtuális hálózatban lévő ügyfelek biztonságosan érhetik el a magánhálózati kapcsolatokon keresztül privát végponti kapcsolattal rendelkező erőforrásokat. További információért látogasson el a következő webhelyre: [https://aka.ms/fhir-privatelink](https://aka.ms/fhir-privatelink) . |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_PrivateLink_Audit.json) |
|[A CORS nem engedheti meg, hogy minden tartomány hozzáférjen a FHIR API-hoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |A több eredetű erőforrás-megosztás (CORS) nem teszi lehetővé, hogy az összes tartomány hozzáférjen az API-hoz a FHIR. Az API-k FHIR való ellátásához távolítsa el az összes tartományhoz való hozzáférést, és explicit módon határozza meg a kapcsolódáshoz engedélyezett tartományokat. |naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
