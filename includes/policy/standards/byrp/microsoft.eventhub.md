---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/08/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 503a64129442907625cc8c4be6f464c1d0a4ba39
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2021
ms.locfileid: "98048087"
---
## <a name="azure-security-benchmark"></a>Azure-biztonsági teljesítményteszt

Az [Azure biztonsági teljesítményteszt](../../../../articles/security/benchmarks/overview.md) az Azure-beli felhőalapú megoldások biztonságossá tételével kapcsolatos ajánlásokat tartalmaz. Ha szeretné megtekinteni, hogy a szolgáltatás hogyan képezi le teljesen az Azure biztonsági Teljesítménytesztét, tekintse meg az [Azure biztonsági teljesítményteszt-leképezési fájljait](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Ha szeretné áttekinteni, hogy az összes Azure-szolgáltatáshoz rendelkezésre álló Azure Policy hogyan jelennek meg a megfelelőségi szabványhoz, tekintse meg a [Azure Policy szabályozási megfelelőség – Azure biztonsági teljesítményteszt](../../../../articles/governance/policy/samples/azure-security-benchmark.md)című témakört.

|Tartomány |Vezérlő azonosítója |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Házirend verziója<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Hálózati biztonság |1,1 |Az erőforrások védelme hálózati biztonsági csoportokkal vagy a Virtual Network Azure Firewall használatával |[Az Event hub-nak virtuális hálózati szolgáltatási végpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|Naplózás és monitorozás |2.3 |Naplózás engedélyezése az Azure-erőforrásokhoz |[Az Event hub diagnosztikai naplóit engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

Ha szeretné áttekinteni, hogy az összes Azure-szolgáltatáshoz rendelkezésre álló Azure Policy hogyan jelennek meg a megfelelőségi szabványhoz, tekintse meg a [Azure Policy szabályozási megfelelőség – HIPAA HITRUST 9,2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md)című témakört.
További információ erről a megfelelőségi szabványról: [HIPAA HITRUST 9,2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

|Tartomány |Vezérlő azonosítója |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Házirend verziója<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Elkülönítés a hálózatokban |0805.01 m1Organizational. 12 – 01. m |A szervezet biztonsági átjárói (például tűzfalak) kikényszerítik a biztonsági házirendeket, és úgy vannak konfigurálva, hogy szűrik a tartományok közötti forgalmat, blokkolják a jogosulatlan hozzáférést, és a belső vezetékes, belső vezeték nélküli és külső hálózati szegmensek (például az Internet) közötti elkülönítést használják, beleértve a DMZ és az egyes tartományok hozzáférés-vezérlési szabályzatának érvényesítését. |[Az Event hub-nak virtuális hálózati szolgáltatási végpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|Elkülönítés a hálózatokban |0806.01 m2Organizational. 12356-01. m |A szervezetek hálózata logikailag és fizikailag szegmentálva van egy meghatározott biztonsági kerületben és egy beosztott vezérlőkkel, beleértve a nyilvánosan elérhető rendszerösszetevők alhálózatait is, amelyek logikailag elkülönülnek a belső hálózattól a szervezeti követelmények alapján. a forgalom ellenőrzése a szükséges funkciókon és az adatok/rendszerek besorolásán alapul, kockázatértékelés és a rájuk vonatkozó biztonsági követelmények alapján. |[Az Event hub-nak virtuális hálózati szolgáltatási végpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|Elkülönítés a hálózatokban |0894.01 m2Organizational. 7 – 01. m |A hálózatok a fizikai kiszolgálók, alkalmazások vagy adatok virtualizált kiszolgálókra való áttelepítése során elkülönítettek a termelési szintű hálózatokból. |[Az Event hub-nak virtuális hálózati szolgáltatási végpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|Naplózás naplózása |1207.09 aa2System. 4-09. AA |A naplózási rekordok megőrzése 90 nap, míg a régebbi naplózási rekordok egy évig archiválva lesznek. |[Az Event hub diagnosztikai naplóit engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |
|Hálózati vezérlők |0863.09 m2Organizational. 910-09. m |A szervezet olyan tűzfal-konfigurációt hoz létre, amely korlátozza a nem megbízható hálózatok és a kezelt információs környezet bármely rendszerösszetevője közötti kapcsolatokat. a tűzfal konfigurációjának változásai a hálózati diagramon is frissülnek. |[Az Event hub-nak virtuális hálózati szolgáltatási végpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |

