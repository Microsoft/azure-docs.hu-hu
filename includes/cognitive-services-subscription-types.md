---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: aedfe8783beacfe2e6679848ef4c2defa24d2da0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "95558486"
---
<!-- > [!NOTE]
> Subscription owners can disable the creation of Cognitive Services resources for resource groups and subscriptions by applying [Azure policy](../articles/governance/policy/overview.md#policy-definition), assigning a “Not allowed resource types” policy definition, and specifying **Microsoft.CognitiveServices/accounts** as the target resource type. -->
Az Azure Cognitive Services két különböző erőforráson keresztül érhető el: több szolgáltatásból álló erőforrás vagy egyetlen szolgáltatás.

* Több szolgáltatásból álló erőforrás:
    * Több Azure-Cognitive Services elérése egyetlen kulccsal és végponttal.
    * Konszolidálja a használatban lévő szolgáltatások számlázását.
* Egy szolgáltatásból származó erőforrás:
    * Egyetlen Azure-beli kognitív szolgáltatás elérése egyedi kulccsal és végponttal minden létrehozott szolgáltatáshoz. 
    * A szolgáltatás kipróbálásához használja az ingyenes szintet.