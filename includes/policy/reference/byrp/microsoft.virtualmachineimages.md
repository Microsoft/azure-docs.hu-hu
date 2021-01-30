---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/29/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c610a9b800a655821149ab6c4f3d6a40f021aebf
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2021
ms.locfileid: "99090895"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A virtuálisgép-rendszerkép-készítő sablonoknak privát hivatkozást kell használniuk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2154edb9-244f-4741-9970-660785bccdaa) |Olyan virtuálisgép-rendszerkép-készítő sablonok naplózása, amelyeken nincs konfigurálva a virtuális hálózat. Ha a virtuális hálózat nincs konfigurálva, a rendszer létrehoz és használ egy nyilvános IP-címet, amely közvetlenül elérhetővé teheti az erőforrásokat az interneten, és növelheti a lehetséges támadási felületet. |Naplózás, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/VM%20Image%20Builder/PrivateLinkEnabled_Audit.json) |
