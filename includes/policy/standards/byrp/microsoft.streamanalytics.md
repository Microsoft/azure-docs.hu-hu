---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: e61c494071cff38008bbfe70821bd385b2bcb530
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872989"
---
## <a name="azure-security-benchmark"></a>Azure-biztonsági teljesítményteszt

Az [Azure biztonsági teljesítményteszt](/security/benchmark/azure/introduction) javaslatokat tesz arra vonatkozóan, hogyan biztosíthatja felhőalapú megoldásait az Azure-ban. A szolgáltatás az Azure biztonsági teljesítményteszthez való teljes leképezését az Azure biztonsági teljesítményteszt leképezési [fájljaiban () láthatja.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

Annak áttekintését, hogy Azure Policy összes Azure-szolgáltatáshoz elérhető beépített funkciók hogyan érhetők el ehhez a megfelelőségi szabványhoz, lásd: Azure Policy jogszabályi megfelelőség – [Azure biztonsági teljesítményteszt.](../../../../articles/governance/policy/samples/azure-security-benchmark.md)

|Tartomány |Vezérlő azonosítója |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Szabályzat verziója<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Naplózás és fenyegetésészlelés |LT-4 |Naplózás engedélyezése Azure-erőforrásokhoz |[Engedélyezni kell a Azure Stream Analytics erőforrásnaplóit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |

## <a name="azure-security-benchmark-v1"></a>Azure biztonsági teljesítményteszt v1

Az [Azure biztonsági teljesítményteszt](/security/benchmark/azure/introduction) javaslatokat tesz arra vonatkozóan, hogyan biztosíthatja felhőalapú megoldásait az Azure-ban. A szolgáltatás az Azure biztonsági teljesítményteszthez való teljes leképezését az Azure biztonsági teljesítményteszt térképfájlja [tartalmazza.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

Annak áttekintését, hogy Azure Policy összes Azure-szolgáltatáshoz elérhető beépített funkciók hogyan érhetők el ehhez a megfelelőségi szabványhoz, tekintse meg a [következőt: Azure Policy Regulatory Compliance - Azure Security Benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md)(Előírásoknak való megfelelőség – Azure biztonsági teljesítményteszt).

|Tartomány |Vezérlő azonosítója |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Szabályzat verziója<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Naplózás és monitorozás |2.3 |Naplózás engedélyezése Azure-erőforrásokhoz |[Engedélyezni kell a Azure Stream Analytics naplóit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |

## <a name="cis-microsoft-azure-foundations-benchmark-130"></a>CIS Microsoft Azure Foundations Benchmark 1.3.0

Ha át Azure Policy az összes Azure-szolgáltatáshoz elérhető beépített funkciókat erre a megfelelőségi szabványra, tekintse meg a [Azure Policy Regulatory Compliance - CIS Microsoft Azure Foundations Benchmark 1.3.0 (A CIS Microsoft Azure Foundations Benchmark 1.3.0](../../../../articles/governance/policy/samples/cis-azure-1-3-0.md)szabványt).
További információ erről a megfelelőségi szabványról: [CIS Microsoft Azure Foundations Benchmark](https://www.cisecurity.org/benchmark/azure/).

|Tartomány |Vezérlőazonosító |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Szabályzatverzió<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Naplózás és monitorozás |5.3 |Győződjön meg arról, hogy a diagnosztikai naplók engedélyezve vannak az azt támogató összes szolgáltatáshoz. |[Engedélyezni kell a Azure Stream Analytics naplóit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |

## <a name="cmmc-level-3"></a>CMMC 3. szint

Annak áttekintését, hogy Azure Policy összes Azure-szolgáltatáshoz elérhető beépített funkciók hogyan érhetők el erre a megfelelőségi szabványra, tekintse meg a Azure Policy cmmc level 3 (A [CMMC 3.](../../../../articles/governance/policy/samples/cmmc-l3.md)szintje) Azure Policy ismertető cikkét.
További információ erről a megfelelőségi szabványról: [Cybersecurity Maturity Model Certification (CMMC).](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf)

|Tartomány |Vezérlőazonosító |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Szabályzatverzió<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Rendszer- és kommunikációvédelem |SC.3.177 |FIPS által ellenőrzött titkosítás használata a CUI titkosságának védelme érdekében. |[Azure Stream Analytics feladatoknak ügyfél által kezelt kulcsokat kell használniuk az adatok titkosításához](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F87ba29ef-1ab3-4d82-b763-87fcd4f531f7) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_CMK_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

Annak áttekintését, Azure Policy az összes Azure-szolgáltatáshoz elérhető beépített funkciók hogyan érhetők el erre a megfelelőségi szabványra, tekintse meg a [Azure Policy compliance - HIPAA HITRUST 9.2 (A HIPAA HITRUST 9.2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md)szabványnak megfelelő szabályozási megfelelőség) cikk nyújt áttekintést.
További információ erről a megfelelőségi szabványról: [HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

|Tartomány |Vezérlőazonosító |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Szabályzatverzió<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Naplózás naplózása |1207.09aa2System.4 – 09.aa |A rendszer 90 napig őrzi meg a naplórekordokat, a régebbi naplórekordokat pedig egy évig archiválja. |[Engedélyezni kell a Azure Stream Analytics naplóit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |

