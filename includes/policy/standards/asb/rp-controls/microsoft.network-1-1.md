---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 2a20898586e49b0bdad97f8c33936a484558dd0d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104590124"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az összes internetes forgalmat az üzembe helyezett Azure Firewall keresztül kell átirányítani](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c) |Azure Security Center azonosította, hogy egyes alhálózatok nem védik a következő generációs tűzfallal. Az alhálózatok elleni védelem a lehetséges fenyegetésekkel szemben, ha korlátozza a hozzáférést Azure Firewall vagy egy támogatott következő generációs tűzfallal |AuditIfNotExists, letiltva |[3.0.0 – előzetes verzió](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ASC_All_Internet_traffic_should_be_routed_via_Azure_Firewall.json) |
|[Az alhálózatokat hálózati biztonsági csoporttal kell társítani](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |A hálózati biztonsági csoporttal (NSG) való hozzáférés korlátozásával védi az alhálózatot az esetleges fenyegetésekkel szemben. A NSG olyan Access Control lista (ACL) szabályok listáját tartalmazza, amelyek engedélyezik vagy megtagadják az alhálózat felé irányuló hálózati forgalmat. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[A virtuális gépeket egy jóváhagyott virtuális hálózathoz kell csatlakoztatni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Ez a házirend a nem jóváhagyott virtuális hálózathoz csatlakoztatott virtuális gépeket naplózza. |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[A virtuális hálózatoknak a megadott virtuális hálózati átjárót kell használniuk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff1776c76-f58c-4245-a8d0-2b207198dc8b) |Ez a házirend minden virtuális hálózatot naplóz, ha az alapértelmezett útvonal nem a megadott virtuális hálózati átjáróra mutat. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetwork_ApprovedVirtualNetworkGateway_AuditIfNotExists.json) |
