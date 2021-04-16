---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 024d5a8b71d269da478bc8a9e3559b1eb4b92c63
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505453"
---
## <a name="cmmc-level-3"></a>CMMC 3. szint

Annak áttekintését, hogy Azure Policy összes Azure-szolgáltatáshoz elérhető beépített funkciók hogyan érhetők el ehhez a megfelelőségi szabványhoz, lásd: Azure Policy jogszabályi megfelelőség – [CMMC 3. szintje.](../../../../articles/governance/policy/samples/cmmc-l3.md)
További információ erről a megfelelőségi szabványról: [Cybersecurity Maturity Model Certification (CMMC)](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf).

|Tartomány |Vezérlő azonosítója |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Szabályzat verziója<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Hozzáférés-vezérlés |AC.1.001 |Korlátozza az információs rendszerekhez való hozzáférést a jogosult felhasználókra, a jogosult felhasználók nevében működő folyamatokra és az eszközökre (beleértve az egyéb információs rendszereket is). |[A CORS nem engedélyezheti, hogy minden tartomány hozzáférjen az API for FHIR-hez](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Hozzáférés-vezérlés |AC.1.002 |Korlátozza az információs rendszerek hozzáférését az arra jogosult felhasználók által végrehajtható tranzakciókra és funkciókra. |[A CORS nem engedélyezheti, hogy minden tartomány hozzáférjen az API for FHIR-hez](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Hozzáférés-vezérlés |AC.2.016 |A CUI folyamatának szabályozása a jóváhagyott engedélyekkel összhangban. |[A CORS nem engedélyezheti, hogy minden tartomány hozzáférjen az API for FHIR-hez](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Konfigurációkezelés |CM.3.068 |A nem szükséges programok, függvények, portok, protokollok és szolgáltatások használatának korlátozása, letiltása vagy megakadályozása. |[A CORS nem engedélyezheti, hogy minden tartomány hozzáférjen az API for FHIR-hez](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Rendszer- és kommunikációvédelem |SC.3.177 |FIPS által ellenőrzött titkosítás használata a CUI titkosságának védelme érdekében. |[Azure API for FHIR felhasználó által felügyelt kulcsot kell használnia az adatok titkosításához](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F051cba44-2429-45b9-9649-46cec11c7119) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_EnableByok_Audit.json) |
|Rendszer- és kommunikációvédelem |SC.3.183 |Alapértelmezés szerint tiltsa le a hálózati kommunikációs forgalmat, és engedélyezze a hálózati kommunikációs forgalmat kivétel alapján (vagyis tiltsa le az összeset, engedélyezze kivétel alapján). |[A CORS nem engedélyezheti, hogy minden tartomány hozzáférjen az API for FHIR-hez](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |

