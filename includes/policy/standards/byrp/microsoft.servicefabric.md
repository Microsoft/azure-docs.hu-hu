---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d39f57fe8b80621a18de4bd502fd5d2efd95bb38
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107499587"
---
## <a name="azure-security-benchmark"></a>Azure-biztonsági teljesítményteszt

Az [Azure biztonsági teljesítményteszt](../../../../articles/security/benchmarks/overview.md) javaslatokat tesz arra vonatkozóan, hogyan biztosíthatja a felhőalapú megoldások biztonságát az Azure-ban. A szolgáltatás az Azure biztonsági teljesítményteszthez való teljes leképezését az Azure biztonsági teljesítményteszt leképezési [fájljaiban () láthatja.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

Annak áttekintését, hogy Azure Policy összes Azure-szolgáltatáshoz elérhető beépített funkciók hogyan érhetők el ehhez a megfelelőségi szabványhoz, tekintse meg a [következőt: Azure Policy Regulatory Compliance - Azure Security Benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md)(Előírásoknak való megfelelőség – Azure biztonsági teljesítményteszt).

|Tartomány |Vezérlő azonosítója |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Szabályzat verziója<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Identitáskezelés |IM-1 |Az Azure Active Directory mint központi identitás- és hitelesítési rendszer szabványosítása |[Service Fabric fürtök csak ügyfél-Azure Active Directory használjanak](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
|Adatvédelem |DP-5 |Bizalmas adatok titkosítása |[Service Fabric fürtök ClusterProtectionLevel tulajdonságát EncryptAndSign beállításra kell állítani](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |

## <a name="azure-security-benchmark-v1"></a>Azure biztonsági teljesítményteszt v1

Az [Azure biztonsági teljesítményteszt](../../../../articles/security/benchmarks/overview.md) javaslatokat tesz arra vonatkozóan, hogyan biztosíthatja a felhőalapú megoldások biztonságát az Azure-ban. A szolgáltatás az Azure biztonsági teljesítményteszthez való teljes leképezését az Azure biztonsági teljesítményteszt leképezési [fájljaiban () láthatja.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

Annak áttekintését, hogy Azure Policy összes Azure-szolgáltatáshoz elérhető beépített funkciók hogyan érhetők el ehhez a megfelelőségi szabványhoz, tekintse meg a [következőt: Azure Policy Regulatory Compliance - Azure Security Benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md)(Előírásoknak való megfelelőség – Azure biztonsági teljesítményteszt).

|Tartomány |Vezérlő azonosítója |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Szabályzat verziója<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Identitás- és hozzáférés-vezérlés |3.9 |Az Azure Active Directory használata |[Service Fabric fürtök csak ügyfél-Azure Active Directory használjanak](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
|Adatvédelem |4.8 |Bizalmas adatok titkosítása |[Service Fabric fürtök ClusterProtectionLevel tulajdonságát EncryptAndSign beállításra kell állítani](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |

## <a name="cmmc-level-3"></a>CMMC 3. szint

Annak áttekintését, hogy Azure Policy összes Azure-szolgáltatáshoz elérhető beépített funkciók hogyan érhetők el ehhez a megfelelőségi szabványhoz, lásd: Azure Policy jogszabályi megfelelőség – [CMMC 3. szintje.](../../../../articles/governance/policy/samples/cmmc-l3.md)
További információ erről a megfelelőségi szabványról: [Cybersecurity Maturity Model Certification (CMMC)](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf).

|Tartomány |Vezérlő azonosítója |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Szabályzat verziója<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Rendszer- és kommunikációvédelem |SC.3.177 |FIPS által ellenőrzött titkosítás használata a CUI titkosságának védelme érdekében. |[Service Fabric fürtök ClusterProtectionLevel tulajdonságát EncryptAndSign beállításra kell állítani](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |
|Rendszer- és kommunikációvédelem |SC.3.191 |A CUI-k bizalmasságának védelme az adatokon. |[Service Fabric fürtök ClusterProtectionLevel tulajdonságát EncryptAndSign beállításra kell állítani](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |

## <a name="iso-270012013"></a>ISO 27001:2013

Annak áttekintését, hogy Azure Policy összes Azure-szolgáltatáshoz elérhető beépített funkciók hogyan érhetők el ehhez a megfelelőségi szabványhoz, lásd: Azure Policy Jogszabályi megfelelőség [– ISO 27001:2013.](../../../../articles/governance/policy/samples/iso-27001.md)
További információ erről a megfelelőségi szabványról: [ISO 27001:2013.](https://www.iso.org/isoiec-27001-information-security.html)

|Tartomány |Vezérlő azonosítója |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Szabályzat verziója<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Hozzáférés-vezérlés |9.2.3 |Az emelt szintű hozzáférési jogosultságok kezelése |[Service Fabric fürtök csak ügyfél-Azure Active Directory használjanak](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
|Titkosítás |10.1.1 |Titkosítási vezérlők használatára vonatkozó szabályzat |[Service Fabric fürtök ClusterProtectionLevel tulajdonságát EncryptAndSign beállításra kell állítani](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |

## <a name="new-zealand-ism-restricted"></a>Új-Zélandi ISM korlátozott

Annak áttekintését, hogy Azure Policy összes Azure-szolgáltatáshoz elérhető beépített funkciók hogyan érhetők el ehhez a megfelelőségi szabványhoz, tekintse meg a következőt: Azure Policy Regulatory Compliance - New Zéland ISM Restricted (Szabályozási megfelelőség – Új-Zéland [isM korlátozott).](../../../../articles/governance/policy/samples/new-zealand-ism.md)
További információ erről a megfelelőségi szabványról: [Új-Zéland ISM korlátozott](https://www.nzism.gcsb.govt.nz/).

|Tartomány |Vezérlő azonosítója |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Szabályzat verziója<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Access Control és jelszavak |AC-2 |16.1.32 Rendszerfelhasználó-azonosítás |[Service Fabric fürtök csak ügyfél-Azure Active Directory használjanak](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |

## <a name="nist-sp-800-53-r4"></a>NIST SP 800-53 R4

Annak áttekintését, hogy Azure Policy összes Azure-szolgáltatáshoz elérhető beépített funkciók hogyan érhetők el ehhez a megfelelőségi szabványhoz, lásd: [Azure Policy Regulatory Compliance - NIST SP 800-53 R4 ( Szabályozási megfelelőség – NIST SP 800-53 R4).](../../../../articles/governance/policy/samples/nist-sp-800-53-r4.md)
További információ erről a megfelelőségi szabványról: [NIST SP 800-53 R4.](https://nvd.nist.gov/800-53)

|Tartomány |Vezérlő azonosítója |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Szabályzat verziója<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Hozzáférés-vezérlés |AC-2 (7) |Fiókkezelési \| Role-Based sémái |[Service Fabric fürtök csak ügyfél-Azure Active Directory használjanak](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |

