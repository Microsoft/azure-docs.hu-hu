---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8e9f2354755a7141d7a156cec4ece73577cfb0cb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587686"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A felügyelt alkalmazás alkalmazás-definíciójának az ügyfél által biztosított Storage-fiókot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9db7917b-1607-4e7d-a689-bca978dd0633) |A saját Storage-fiók használatával szabályozhatja az alkalmazás-definíció adatait, ha ez egy szabályozási vagy megfelelőségi követelmény. Megadhatja, hogy a felügyelt alkalmazás definícióját a létrehozás során megadott Storage-fiókon belül tárolja, így a hely és a hozzáférés teljes mértékben felügyelhető a szabályozási megfelelőségi követelmények teljesítése érdekében. |naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/ApplicationDefinition_Missing_StorageAccount_Deny.json) |
|[Társítások telepítése felügyelt alkalmazásokhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17763ad9-70c0-4794-9397-53d765932634) |Üzembe helyez egy olyan társítási erőforrást, amely a kiválasztott erőforrás-típusokat társítja a megadott felügyelt alkalmazáshoz.  Ez a házirend-telepítés nem támogatja a beágyazott erőforrástípusok használatát. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/AssociationForManagedApplication_Deploy.json) |
