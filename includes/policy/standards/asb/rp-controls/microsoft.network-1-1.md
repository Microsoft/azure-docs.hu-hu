---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7a9e461aab44e523bade31c8a679c258f8706ab1
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107513582"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Minden internetes forgalmat az üzembe helyezett virtuális gépre kell Azure Firewall](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c) |Azure Security Center azonosította, hogy egyes alhálózatokat nem véd a következő generációs tűzfal. Védje meg az alhálózatokat a lehetséges fenyegetésekkel szemben azáltal, hogy korlátozza a Azure Firewall vagy egy támogatott következő generációs tűzfallal |AuditIfNotExists, Disabled |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ASC_All_Internet_traffic_should_be_routed_via_Azure_Firewall.json) |
|[Az alhálózatokat egy hálózati biztonsági csoporthoz kell hozzárendelni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Egy hálózati biztonsági csoporttal (NSG) korlátozhatja az alhálózat védelmét a lehetséges fenyegetésekkel szemben. Az NSG-k az alhálózatra Access Control (ACL) vonatkozó szabályok listáját tartalmazzák, amelyek engedélyezik vagy megtagadják az alhálózatra való hálózati forgalmat. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[A virtuális gépeket egy jóváhagyott virtuális hálózathoz kell csatlakoztatni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Ez a szabályzat minden olyan virtuális gépet naplól, amely nem jóváhagyott virtuális hálózathoz csatlakozik. |Naplózás, Megtagadás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[A virtuális hálózatoknak a megadott virtuális hálózati átjárót kell használniuk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff1776c76-f58c-4245-a8d0-2b207198dc8b) |Ez a szabályzat naplót végez minden virtuális hálózatban, ha az alapértelmezett útvonal nem mutat a megadott virtuális hálózati átjáróra. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetwork_ApprovedVirtualNetworkGateway_AuditIfNotExists.json) |
