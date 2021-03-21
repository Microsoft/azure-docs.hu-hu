---
ms.openlocfilehash: 2e92d150851c74a84f785d1f5f0ebe2e5870a54e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97936760"
---


| Szolgáltatás |[Használatalapú csomag](../articles/azure-functions/consumption-plan.md)|[Prémium szintű csomag](../articles/azure-functions/functions-premium-plan.md)|[Dedikált terv](../articles/azure-functions/dedicated-plan.md)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Bejövő IP-korlátozások és privát webhely-hozzáférés](../articles/azure-functions/functions-networking-options.md#inbound-access-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|✅Yes|
|[Virtuális hálózat integrációja](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌No|✅Igen (regionális)|✅Igen (regionális és átjáró)|✅Yes| ✅Yes|
|[Virtuális hálózati eseményindítók (nem HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌No| ✅Yes |✅Yes|✅Yes|✅Yes|
|[Hibrid kapcsolatok](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (csak Windows)|❌No|✅Yes|✅Yes|✅Yes|✅Yes|
|[Kimenő IP-korlátozások](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌No| ✅Yes|✅Yes|✅Yes|✅Yes|