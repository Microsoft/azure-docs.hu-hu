---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0bde67a3082ce7b188179bbe78dc2e09a6a32f17
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107880757"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az internetre néző virtuális gépeket hálózati biztonsági csoportokkal kell védeni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |A hálózati biztonsági csoportokkal (NSG-k) való hozzáférés korlátozásával megvédheti virtuális gépeit a lehetséges fenyegetésektől. További információ az NSG-k forgalmának szabályozásával kapcsolatban: [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[A virtuális gépen az IP-továbbítást le kell tiltani](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd352bd5-2853-4985-bf0d-73806b4a5744) |A virtuális gép hálózati adapterének IP-továbbítása lehetővé teszi, hogy a gép fogadja a más célhelyre címzett forgalmat. Ritkán van szükség IP-továbbításra (például ha a virtuális gépet hálózati virtuális berendezésként használja), ezért a hálózati biztonsági csapatnak át kell vizsgálnia ezt. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_IPForwardingOnVirtualMachines_Audit.json) |
|[A felügyeleti portokat be kell zárni a virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |A nyitott távfelügyeleti portok magas szintű kockázatot jelentenek a virtuális gép számára az internetalapú támadásokkal szemben. Ezek a támadások a hitelesítő adatok találgatásos támadását kísérelik meg, hogy rendszergazdai hozzáférést szerezzenek a géphez. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |
