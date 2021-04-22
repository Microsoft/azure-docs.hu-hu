---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 1e6de5fbca19fb8509c2bc84b53daa8618011b99
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877990"
---
## <a name="azure-security-benchmark"></a>Azure-biztonsági teljesítményteszt

Az [Azure biztonsági teljesítményteszt](/security/benchmark/azure/introduction) javaslatokat tesz arra vonatkozóan, hogyan biztosíthatja a felhőalapú megoldások biztonságát az Azure-ban. A szolgáltatás az Azure biztonsági teljesítményteszthez való teljes leképezését az Azure biztonsági teljesítményteszt térképfájlja [tartalmazza.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

Annak áttekintését, hogy Azure Policy összes Azure-szolgáltatáshoz elérhető beépített funkciók hogyan érhetők el ehhez a megfelelőségi szabványhoz, tekintse meg a [következőt: Azure Policy Regulatory Compliance - Azure Security Benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md)(Előírásoknak való megfelelőség – Azure biztonsági teljesítményteszt).

|Tartomány |Vezérlő azonosítója |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Szabályzat verziója<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Biztonsági másolat és helyreállítás |BR-1 |Rendszeres automatikus biztonsági mentések biztosítása |[Azure Backup engedélyezni kell a Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|Biztonsági másolat és helyreállítás |BR-2 |Biztonsági mentési adatok titkosítása |[Azure Backup engedélyezni kell a Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

## <a name="azure-security-benchmark-v1"></a>Azure biztonsági teljesítményteszt v1

Az [Azure biztonsági teljesítményteszt](/security/benchmark/azure/introduction) javaslatokat tesz arra vonatkozóan, hogyan biztosíthatja a felhőalapú megoldások biztonságát az Azure-ban. A szolgáltatás az Azure biztonsági teljesítményteszthez való teljes leképezését az Azure biztonsági teljesítményteszt térképfájlja [tartalmazza.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

Annak áttekintését, hogy Azure Policy összes Azure-szolgáltatáshoz elérhető beépített funkciók hogyan érhetők el ehhez a megfelelőségi szabványhoz, lásd: Azure Policy jogszabályi megfelelőség – [Azure biztonsági teljesítményteszt.](../../../../articles/governance/policy/samples/azure-security-benchmark.md)

|Tartomány |Vezérlő azonosítója |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Szabályzatverzió<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Adat-helyreállítás |9.1 |Rendszeres automatikus biztonságimentás-frissítések biztosítása |[Azure Backup engedélyezni kell a Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|Adat-helyreállítás |9,2 |A rendszer teljes biztonsági mentése és az ügyfelek által kezelt kulcsok biztonsági mentése |[Azure Backup engedélyezni kell a Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

## <a name="cmmc-level-3"></a>CMMC 3. szint

Annak áttekintését, hogy Azure Policy összes Azure-szolgáltatáshoz elérhető beépített funkciók hogyan érhetők el erre a megfelelőségi szabványra, tekintse meg a Azure Policy cmmc level 3 (A [CMMC 3.](../../../../articles/governance/policy/samples/cmmc-l3.md)szintje) Azure Policy ismertető cikkét.
További információ erről a megfelelőségi szabványról: [Cybersecurity Maturity Model Certification (CMMC).](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf)

|Tartomány |Vezérlőazonosító |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Szabályzatverzió<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Helyreállítási |RE.2.137 |Az adatok biztonsági frissítésének rendszeres végrehajtása és tesztelése. |[Azure Backup engedélyezni kell a Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|Helyreállítási |RE.3.139 |Rendszeresen végezzen teljes, átfogó és rugalmas biztonsági mentéseket az adatokról szervezetileg meghatározott módon. |[Azure Backup engedélyezni kell a Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

Annak áttekintését, hogy az Azure Policy összes Azure-szolgáltatáshoz elérhető beépített funkciók hogyan érhetők el ehhez a megfelelőségi szabványhoz, lásd: [Azure Policy regulatory compliance - HIPAA HITRUST 9.2 (Szabályozási megfelelőség – HIPAA HITRUST 9.2).](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md)
További információ erről a megfelelőségi szabványról: [HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

|Tartomány |Vezérlő azonosítója |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Szabályzat verziója<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Biztonságimentás |1620.09l1Szervezeti.8 – 09.l |Ha a biztonsági mentési szolgáltatást a harmadik fél kézbesíti, a szolgáltatói szerződés részletes védelmet biztosít a biztonsági mentési információk titkosságának, integritásának és rendelkezésre állásának szabályozására. |[Azure Backup engedélyezni kell a Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|Biztonságimentás |1625.09l3Szervezeti.34 – 09.l |A rendszer három (3) generációs biztonsági mentést (teljes és az összes kapcsolódó növekményes vagy különbségi biztonsági mentést) tárol a helyszínen, és a helyszíni és a nem helyszíni biztonsági másolatokat is névvel, dátummal, időponttal és művelettel naplózza. |[Azure Backup engedélyezni kell a Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|Biztonságimentás |1699.09l1Szervezeti.10 – 09.l |A munkaerő tagjai azonosítják és közlik a munkaerővel az adatok biztonsági mentési folyamatának szerepköreit és feladatait; A saját eszközök használata (BYOD) felhasználóinak kell biztonsági másolatot készíteniük a szervezeti és/vagy ügyféladatokról az eszközeiken. |[Azure Backup engedélyezni kell a Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

