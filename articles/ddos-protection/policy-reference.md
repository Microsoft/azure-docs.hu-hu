---
title: Beépített szabályzat-definíciók a Azure DDoS Protection standardhoz
description: A Azure DDoS Protection standard Azure Policy beépített szabályzat-definícióit sorolja fel. Ezek a beépített szabályzat-definíciók közös megközelítéseket biztosítanak az Azure-erőforrások kezeléséhez.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/07/2021
ms.author: yitoh
ms.custom: subject-policy-reference
ms.topic: include
ms.openlocfilehash: 6a0496740fbd82090ba7199ca3e064f5bd2bf7c5
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108267"
---
# <a name="azure-policy-built-in-definitions-for-azure-ddos-protection-standard"></a>A Azure DDoS Protection standard Azure Policy beépített definíciói

Ez az oldal a Azure DDoS Protection standard [Azure Policy](../governance/policy/overview.md) beépített szabályzat-definícióinak indexe. További Azure Policy a más szolgáltatásokhoz készült beépített [definíciók: Azure Policy beépített definíciók](../governance/policy/samples/built-in-policies.md).

Az egyes beépített szabályzat-definíciók neve a Azure Portal házirend-definíciójának nevére hivatkozik. A **verzió** oszlopban található hivatkozás használatával megtekintheti a forrást a [Azure Policy GitHub](https://github.com/Azure/azure-policy)-tárházban.

## <a name="azure-ddos-protection-standard"></a>Azure DDoS Protection Standard

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A virtuális hálózatokat Azure DDoS Protection standard szintű védelemmel kell ellátni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F94de2ad3-e0c1-4caf-ad78-5d47bbc83d3d)|A virtuális hálózatok a Azure DDoS Protection standard szintű és a protokollok elleni támadásokkal védhetők. További információért látogasson el a következő oldalra: [https://aka.ms/ddosprotectiondocs](https://aka.ms/ddosprotectiondocs) .|Módosítás, naplózás, letiltva|[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkDdosStandard_Audit.json)|
|[A nyilvános IP-címekhez engedélyezni kell a Azure DDoS Protection standard szintű erőforrás-naplókat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F752154a7-1e0f-45c6-a880-ac75a7e4f648)|Engedélyezze az erőforrás-naplókat a nyilvános IP-címekhez a diagnosztikai beállításokban Log Analytics munkaterületre való továbbításhoz. Részletes láthatóságot kaphat a támadási forgalomról, valamint a DDoS-támadások az értesítéseken, jelentéseken és adatforgalmi naplókban való enyhítésére tett műveletekről.|AuditIfNotExists, DeployIfNotExists, letiltva|[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/PublicIpDdosLogging_Audit.json)|


## <a name="next-steps"></a>További lépések

- A beépített elemek megtekintése az [Azure Policy GitHub-adattárában](https://github.com/Azure/azure-policy).
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../governance/policy/concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../governance/policy/concepts/effects.md).