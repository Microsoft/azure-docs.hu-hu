---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 6ad3c1bdf16eee4adc285cbe4d78f20953cda5a6
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102616199"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A virtuálisgép-rendszerkép-készítő sablonoknak privát hivatkozást kell használniuk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2154edb9-244f-4741-9970-660785bccdaa) |Olyan virtuálisgép-rendszerkép-készítő sablonok naplózása, amelyeken nincs konfigurálva a virtuális hálózat. Ha a virtuális hálózat nincs konfigurálva, a rendszer létrehoz és használ egy nyilvános IP-címet, amely közvetlenül elérhetővé teheti az erőforrásokat az interneten, és növelheti a lehetséges támadási felületet. |Naplózás, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/VM%20Image%20Builder/PrivateLinkEnabled_Audit.json) |
