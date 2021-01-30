---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/29/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: ab5955517331744a3b3ea7d2e8929e2651e24373
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2021
ms.locfileid: "99214126"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A FHIR készült Azure API-nak ügyfél által felügyelt kulcsot (CMK) kell használnia a REST-adatok titkosításához](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F051cba44-2429-45b9-9649-46cec11c7119) |A FHIR Azure API-ban tárolt adatok titkosítását az ügyfél által felügyelt kulcs segítségével szabályozhatja, ha ez szabályozási vagy megfelelőségi követelmény. Az ügyfél által felügyelt kulcsok emellett kettős titkosítást is biztosítanak, ha egy második titkosítási réteget adnak hozzá, amely a szolgáltatás által felügyelt kulcsokkal együtt az alapértelmezetten történik. |naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_EnableByok_Audit.json) |
|[A FHIR készült Azure API-nak privát hivatkozást kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ee56206-5dd1-42ab-b02d-8aae8b1634ce) |A FHIR készült Azure API-nak legalább egy jóváhagyott privát végponti kapcsolatban kell lennie. A virtuális hálózatban lévő ügyfelek biztonságosan érhetik el a magánhálózati kapcsolatokon keresztül privát végponti kapcsolattal rendelkező erőforrásokat. További információért látogasson el a következő webhelyre: [https://aka.ms/fhir-privatelink](https://aka.ms/fhir-privatelink) . |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_PrivateLink_Audit.json) |
|[A CORS nem engedheti meg, hogy minden tartomány hozzáférjen a FHIR API-hoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |A több eredetű erőforrás-megosztás (CORS) nem teszi lehetővé, hogy az összes tartomány hozzáférjen az API-hoz a FHIR. Az API-k FHIR való ellátásához távolítsa el az összes tartományhoz való hozzáférést, és explicit módon határozza meg a kapcsolódáshoz engedélyezett tartományokat. |naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
