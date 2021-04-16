---
title: Beépített szabályzatdefiníciók a Azure DDoS Protection Standardhoz
description: A Azure Policy standard Azure DDoS Protection beépített szabályzatdefinícióit sorolja fel. Ezek a beépített szabályzatdefiníciók gyakori megközelítéseket biztosítanak az Azure-erőforrások kezeléséhez.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2021
ms.author: yitoh
ms.custom: subject-policy-reference
ms.topic: include
ms.openlocfilehash: 86fa383fe2456eabe0cce142bd4a9c665fb95a2a
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505700"
---
# <a name="azure-policy-built-in-definitions-for-azure-ddos-protection-standard"></a>Azure Policy Standard Azure DDoS Protection beépített definíciói

Ez az oldal a Standard [Azure Policy](../governance/policy/overview.md) szabályzatdefiníciók beépített Azure DDoS Protection indexe. Az egyéb Azure Policy további beépített szolgáltatásaiért lásd a Azure Policy [definíciókat.](../governance/policy/samples/built-in-policies.md)

Az egyes beépített szabályzatdefiníciók neve a szabályzatdefinícióra mutató hivatkozásokat tartalmaz a Azure Portal. A Verzió oszlopban található **hivatkozással** megtekintheti a forrást a [GitHub Azure Policy tárban.](https://github.com/Azure/azure-policy)

## <a name="azure-ddos-protection-standard"></a>Azure DDoS Protection Standard

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A virtuális hálózatokat a standard Azure DDoS Protection védeni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F94de2ad3-e0c1-4caf-ad78-5d47bbc83d3d)|A Standard használatával megvédheti virtuális hálózatait a mennyiségi és protokolltámadásokkal Azure DDoS Protection támadásoktól. További információ: [https://aka.ms/ddosprotectiondocs](https://aka.ms/ddosprotectiondocs) .|Módosítás, Naplózás, Letiltva|[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkDdosStandard_Audit.json)|
|[A nyilvános IP-címeken engedélyezni kell az erőforrásnaplókat a standard Azure DDoS Protection számára](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F752154a7-1e0f-45c6-a880-ac75a7e4f648)|Engedélyezze a nyilvános IP-címek erőforrásnaplóit a diagnosztikai beállításokban a Log Analytics-munkaterületre való streameléshez. Értesítések, jelentések és folyamatnaplók segítségével részletes betekintést kaphat a támadási forgalomba és a DDoS-támadások mérséklése érdekében tett műveletekbe.|AuditIfNotExists, DeployIfNotExists, Disabled|[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/PublicIpDdosLogging_Audit.json)|


## <a name="next-steps"></a>További lépések

- A beépített elemek megtekintése az [Azure Policy GitHub-adattárában](https://github.com/Azure/azure-policy).
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../governance/policy/concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../governance/policy/concepts/effects.md).
