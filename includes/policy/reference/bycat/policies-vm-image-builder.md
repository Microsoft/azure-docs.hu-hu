---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 4a86ac3ddd639bac7a04d9f2d3bddd40a0774ca2
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106091233"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A virtuálisgép-rendszerkép-készítő sablonoknak privát hivatkozást kell használniuk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2154edb9-244f-4741-9970-660785bccdaa) |Az Azure Private link használatával a virtuális hálózatot az Azure-szolgáltatásokhoz nyilvános IP-cím nélkül csatlakoztathatja a forráson vagy a célhelyen. A privát kapcsolati platform kezeli a fogyasztó és a szolgáltatások közötti kapcsolatot az Azure gerinc hálózaton. Ha privát végpontokat rendel a virtuálisgép-rendszerkép Builder-létrehozási erőforrásaihoz, a rendszer csökkenti az adatszivárgás kockázatait. További információ a privát hivatkozásokról: [https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-networking#deploy-using-an-existing-vnet](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-networking#deploy-using-an-existing-vnet) . |Naplózás, letiltva, megtagadás |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/VM%20Image%20Builder/PrivateLinkEnabled_Audit.json) |
