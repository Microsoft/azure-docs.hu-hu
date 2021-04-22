---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 28de9d80a1e5568b70f48e789233af38388fbe70
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866656"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure API for FHIR felhasználó által kezelt kulcsot kell használnia az adatok titkosításához](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F051cba44-2429-45b9-9649-46cec11c7119) |Az ügyfél által felügyelt kulccsal szabályozhatja a titkosítást a Azure API for FHIR tárolt adatok többi részében, ha ez szabályozási vagy megfelelőségi követelmény. Az ügyfél által kezelt kulcsok a dupla titkosítást is biztosítják azáltal, hogy hozzáadnak egy második titkosítási réteget a szolgáltatás által kezelt kulcsokkal végzett alapértelmezett titkosítási réteghez. |naplózás, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_EnableByok_Audit.json) |
|[Azure API for FHIR kapcsolat használata](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ee56206-5dd1-42ab-b02d-8aae8b1634ce) |Azure API for FHIR legalább egy jóváhagyott privát végponti kapcsolattal kell rendelkezik. A virtuális hálózatban lévő ügyfelek privát kapcsolaton keresztül biztonságosan hozzáférhetnek a privát végponti kapcsolattal rendelkezik erőforrásokhoz. További információ: [https://aka.ms/fhir-privatelink](https://aka.ms/fhir-privatelink) . |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_PrivateLink_Audit.json) |
|[A CORS nem engedélyezheti, hogy minden tartomány hozzáférjen az API for FHIR-hez](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |Az eredetközi erőforrás-megosztás (CORS) nem engedélyezheti minden tartomány számára az API for FHIR-hez való hozzáférést. Az API for FHIR védelméhez távolítsa el az összes tartomány hozzáférését, és explicit módon határozza meg a csatlakozáshoz engedélyezett tartományokat. |naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
