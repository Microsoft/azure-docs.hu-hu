---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7345c6078767b0fa807c546d6bcec42a10cb5573
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "107284859"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az adaptív hálózatokra vonatkozó kötési javaslatokat az internetre irányuló virtuális gépeken kell alkalmazni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center elemzi az internetre irányuló virtuális gépek forgalmi mintáit, és hálózati biztonsági csoportokra vonatkozó ajánlásokat biztosít a lehetséges támadási felület csökkentése érdekében. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[Az internetre irányuló virtuális gépeket hálózati biztonsági csoportokkal kell védeni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |A hálózati biztonsági csoportokkal (NSG) való hozzáférés korlátozásával védi a virtuális gépeket a lehetséges fenyegetésekkel szemben. További információ a forgalom szabályozásáról a NSG-on [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[A virtuális gépen lévő IP-továbbítást le kell tiltani](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd352bd5-2853-4985-bf0d-73806b4a5744) |Ha engedélyezi az IP-továbbítást a virtuális gép hálózati adapterén, lehetővé teszi, hogy a gép a többi célhelyre irányuló forgalmat fogadjon. Az IP-továbbítás csak ritkán szükséges (például a virtuális gép hálózati virtuális berendezésként való használata esetén), ezért ezt a hálózati biztonsági csapatnak kell áttekintenie. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_IPForwardingOnVirtualMachines_Audit.json) |
|[A virtuális gépek felügyeleti portjait az igény szerinti hálózati hozzáférés-vezérléssel kell védeni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |A lehető legrövidebb időn belül (JIT) való hozzáférést a Azure Security Center a javaslatok szerint figyeli |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[A felügyeleti portokat be kell zárni a virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |A távoli felügyeleti portok megnyitásakor a virtuális gép nagy mértékben veszélyezteti az internetes támadások kockázatát. Ezek a támadások kipróbálták a hitelesítő adatokat, hogy rendszergazdai hozzáférést kapjanak a géphez. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |
