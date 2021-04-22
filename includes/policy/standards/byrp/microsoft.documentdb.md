---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 222a04afb0b9e1b0b8cb7f8bf2d300790b0c86fe
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873727"
---
## <a name="azure-security-benchmark"></a>Azure-biztonsági teljesítményteszt

Az [Azure biztonsági teljesítményteszt](/security/benchmark/azure/introduction) javaslatokat tesz arra vonatkozóan, hogyan biztosíthatja a felhőalapú megoldások biztonságát az Azure-ban. A szolgáltatás az Azure biztonsági teljesítményteszthez való teljes leképezését az Azure biztonsági teljesítményteszt térképfájlja [tartalmazza.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

Annak áttekintését, hogy Azure Policy összes Azure-szolgáltatáshoz elérhető beépített funkciók hogyan érhetők el ehhez a megfelelőségi szabványhoz, tekintse meg a [következőt: Azure Policy Regulatory Compliance - Azure Security Benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md)(Előírásoknak való megfelelőség – Azure biztonsági teljesítményteszt).

|Tartomány |Vezérlő azonosítója |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Szabályzat verziója<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Hálózati biztonság |NS-1 |A belső forgalom biztonságának megvalósítása |[Azure Cosmos DB fiókoknak tűzfalszabályokkal kell rendelkezőknek lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F862e97cf-49fc-4a5c-9de4-40d4e2e7c8eb) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_NetworkRulesExist_Audit.json) |
|Hálózati biztonság |NS-4 |Alkalmazások és szolgáltatások védelme külső hálózati támadásokkal szemben |[Azure Cosmos DB fiókoknak tűzfalszabályokkal kell rendelkezőknek lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F862e97cf-49fc-4a5c-9de4-40d4e2e7c8eb) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_NetworkRulesExist_Audit.json) |
|Adatvédelem |DP-5 |Bizalmas adatok titkosítása |[Azure Cosmos DB fióknak ügyfél által kezelt kulcsokat kell használnia az adatok titkosításához](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f905d99-2ab7-462c-a6b0-f709acca6c8f) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_CMK_Deny.json) |

## <a name="azure-security-benchmark-v1"></a>Azure biztonsági teljesítményteszt v1

Az [Azure biztonsági teljesítményteszt](/security/benchmark/azure/introduction) javaslatokat tesz arra vonatkozóan, hogyan biztosíthatja felhőalapú megoldásait az Azure-ban. A szolgáltatás az Azure biztonsági teljesítményteszthez való teljes leképezését az Azure biztonsági teljesítményteszt térképfájlja [tartalmazza.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

Annak áttekintését, hogy az Azure Policy összes Azure-szolgáltatáshoz elérhető beépített funkciók hogyan érhetők el erre a megfelelőségi szabványra, tekintse meg a Azure Policy való megfelelésről – [Azure biztonsági teljesítményteszt](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Tartomány |Vezérlőazonosító |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Szabályzatverzió<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Hálózati biztonság |1.1 |Erőforrások védelme hálózati biztonsági csoportokkal vagy Azure Firewall a Virtual Network |[Cosmos DB hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |

## <a name="cmmc-level-3"></a>CMMC 3. szint

Annak áttekintését, hogy Azure Policy összes Azure-szolgáltatáshoz elérhető beépített funkciók hogyan érhetők el erre a megfelelőségi szabványra, tekintse meg a Azure Policy cmmc level 3 (A [CMMC 3.](../../../../articles/governance/policy/samples/cmmc-l3.md)szintje) Azure Policy ismertető cikkét.
További információ erről a megfelelőségi szabványról: [Cybersecurity Maturity Model Certification (CMMC).](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf)

|Tartomány |Vezérlőazonosító |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Szabályzatverzió<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Incidensmegoldás |IR.2.093 |Események észlelése és jelentése. |[Az Advanced Threat Protection üzembe helyezése Cosmos DB fiókokhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb5f04e03-92a3-4b09-9410-2cc5e5047656) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/CosmosDbAdvancedThreatProtection_Deploy.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

Annak áttekintését, Azure Policy az összes Azure-szolgáltatáshoz elérhető beépített funkciók hogyan érhetők el erre a megfelelőségi szabványra, tekintse meg a [Azure Policy compliance - HIPAA HITRUST 9.2 (A HIPAA HITRUST 9.2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md)szabványnak megfelelő szabályozási megfelelőség) cikk nyújt áttekintést.
További információ erről a megfelelőségi szabványról: [HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

|Tartomány |Vezérlő azonosítója |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Szabályzat verziója<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Elkülönítés a hálózatokban |0805.01m1Szervezeti.12 – 01.m |A szervezet biztonsági átjárói (például tűzfalak) biztonsági házirendeket kényszerítnek ki, és úgy vannak konfigurálva, hogy szűrik a tartományok közötti forgalmat, blokkolják a jogosulatlan hozzáférést, és a belső vezetékes, belső vezeték nélküli és külső hálózati szegmensek (például az internet) közötti elkülönítés fenntartására használatosak, beleértve a DMZ-ket is, valamint hozzáférés-vezérlési szabályzatok kényszerítését minden tartományra. |[Cosmos DB hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|Elkülönítés a hálózatokban |0806.01m2Organizational.12356 - 01.m |A szervezeti hálózat logikailag és fizikailag szegmentálva van egy meghatározott biztonsági szegélyhálózattal és egy fokozatos vezérlőkészlettel, beleértve a nyilvánosan elérhető rendszerösszetevők alhálózatát, amelyek a szervezeti követelmények alapján logikailag elkülönülnek a belső hálózattól; A és a forgalom vezérlése a szükséges funkciók, valamint az adatok/rendszerek besorolása alapján történik a kockázatértékelés és a vonatkozó biztonsági követelmények alapján. |[Cosmos DB hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|Elkülönítés a hálózatokban |0894.01m2Szervezeti.7 – 01.m |A fizikai kiszolgálók, alkalmazások vagy adatok virtualizált kiszolgálókra való áttelepítésekor a hálózatok elkülönülnek az éles szintű hálózatoktól. |[Cosmos DB hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|Hálózati vezérlők |0864.09m2Organizational.12 - 09.m |A használati korlátozásokat és a megvalósítási útmutatót hivatalosan definiálják a VoIP-hez, beleértve a szolgáltatás hitelesítését és monitorozását. |[Cosmos DB hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |

