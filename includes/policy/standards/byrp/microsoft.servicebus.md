---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7a64c3558fb96e55bff2a822ed20b45a76d53545
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876194"
---
## <a name="azure-security-benchmark"></a>Azure-biztonsági teljesítményteszt

Az [Azure biztonsági teljesítményteszt](/security/benchmark/azure/introduction) javaslatokat tesz arra vonatkozóan, hogyan biztosíthatja felhőalapú megoldásait az Azure-ban. A szolgáltatás az Azure biztonsági teljesítményteszthez való teljes leképezését az Azure biztonsági teljesítményteszt térképfájlja [tartalmazza.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

Annak áttekintését, hogy az Azure Policy összes Azure-szolgáltatáshoz elérhető beépített funkciók hogyan érhetők el erre a megfelelőségi szabványra, tekintse meg a Azure Policy való megfelelésről – [Azure biztonsági teljesítményteszt](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Tartomány |Vezérlőazonosító |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Szabályzatverzió<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Naplózás és fenyegetésészlelés |LT-4 |Naplózás engedélyezése Azure-erőforrásokhoz |[Engedélyezni kell a Service Bus naplóit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |

## <a name="azure-security-benchmark-v1"></a>Azure biztonsági teljesítményteszt v1

Az [Azure biztonsági teljesítményteszt](/security/benchmark/azure/introduction) javaslatokat tesz arra vonatkozóan, hogyan biztosíthatja felhőalapú megoldásait az Azure-ban. A szolgáltatás az Azure biztonsági teljesítményteszthez való teljes leképezését az Azure biztonsági teljesítményteszt térképfájlja [tartalmazza.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

Annak áttekintését, hogy az Azure Policy összes Azure-szolgáltatáshoz elérhető beépített funkciók hogyan érhetők el erre a megfelelőségi szabványra, tekintse meg a Azure Policy való megfelelésről – [Azure biztonsági teljesítményteszt](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Tartomány |Vezérlőazonosító |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Szabályzatverzió<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Hálózati biztonság |1.1 |Erőforrások védelme hálózati biztonsági csoportokkal vagy Azure Firewall a Virtual Network |[Service Bus hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|Naplózás és monitorozás |2.3 |Naplózás engedélyezése Azure-erőforrásokhoz |[Engedélyezni kell a Service Bus naplóit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |

## <a name="cis-microsoft-azure-foundations-benchmark-130"></a>CIS Microsoft Azure Foundations Benchmark 1.3.0

Annak áttekintését, Azure Policy elérhető beépített funkciók hogyan érhetők el az összes Azure-szolgáltatáshoz ehhez a megfelelőségi szabványhoz, lásd: Azure Policy Jogszabályi megfelelőség [– CIS Microsoft Azure Foundations Benchmark 1.3.0.](../../../../articles/governance/policy/samples/cis-azure-1-3-0.md)
További információ erről a megfelelőségi szabványról: [CIS Microsoft Azure Foundations Benchmark](https://www.cisecurity.org/benchmark/azure/).

|Tartomány |Vezérlő azonosítója |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Szabályzat verziója<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Naplózás és monitorozás |5.3 |Győződjön meg arról, hogy a diagnosztikai naplók engedélyezve vannak az azt támogató összes szolgáltatáshoz. |[Engedélyezni kell a Service Bus naplóit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

Annak áttekintését, hogy Azure Policy összes Azure-szolgáltatáshoz elérhető beépített funkciók hogyan érhetők el ehhez a megfelelőségi szabványhoz, lásd: [Azure Policy regulatory compliance - HIPAA HITRUST 9.2 ( szabályozási megfelelőség – HIPAA HITRUST 9.2).](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md)
További információ erről a megfelelőségi szabványról: [HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

|Tartomány |Vezérlő azonosítója |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Szabályzatverzió<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Elkülönítés a hálózatokban |0805.01m1Organizational.12 - 01.m |A szervezet biztonsági átjárói (például tűzfalak) biztonsági házirendeket kényszerítnek ki, és úgy vannak konfigurálva, hogy szűrik a tartományok közötti forgalmat, blokkolják a jogosulatlan hozzáférést, és a belső vezetékes, belső vezeték nélküli és külső hálózati szegmensek (például az internet) közötti elkülönítés fenntartására használatosak, beleértve a DMZ-ket is, és minden tartományra érvényes hozzáférés-vezérlési házirendeket kényszerítsen ki. |[Service Bus hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|Elkülönítés a hálózatokban |0806.01m2Organizational.12356 - 01.m |A szervezeti hálózat logikailag és fizikailag szegmentálva van egy meghatározott biztonsági szegélyhálózattal és egy fokozatos vezérlőkészlettel, beleértve a nyilvánosan elérhető, a belső hálózattól logikailag elkülönített rendszerösszetevők alhálózatát a szervezeti követelmények alapján; A és a forgalom a szükséges funkciók és az adatok/rendszerek besorolása alapján van szabályozva a kockázatértékelés és a vonatkozó biztonsági követelmények alapján. |[Service Bus hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|Elkülönítés a hálózatokban |0894.01m2Organizational.7 - 01.m |A fizikai kiszolgálók, alkalmazások vagy adatok virtualizált kiszolgálókra való áttelepítésekor a hálózatok elkülönülnek az éles szintű hálózatoktól. |[Service Bus hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|Naplózás naplózása |1208.09aa3System.1 – 09.aa |Az auditnaplók a felügyeleti tevékenységek, a rendszer- és alkalmazásindítási/-leállítási/-hibák, a fájlváltozások és a biztonsági házirendek módosításai esetén vannak megőrizve. |[Engedélyezni kell a Service Bus naplóit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|Hálózati vezérlők |0860.09m1Szervezeti.9 – 09.m |A szervezet hivatalosan felügyeli a hálózaton található berendezéseket, beleértve a felhasználói területek berendezéseit is. |[Service Bus hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |

