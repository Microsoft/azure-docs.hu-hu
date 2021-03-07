---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/05/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7f3f586bf8855e16d68b166fd89eddcdf9f6e400
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102428922"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A felügyelt alkalmazás alkalmazás-definíciójának az ügyfél által biztosított Storage-fiókot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9db7917b-1607-4e7d-a689-bca978dd0633) |A saját Storage-fiók használatával szabályozhatja az alkalmazás-definíció adatait, ha ez egy szabályozási vagy megfelelőségi követelmény. Megadhatja, hogy a felügyelt alkalmazás definícióját a létrehozás során megadott Storage-fiókon belül tárolja, így a hely és a hozzáférés teljes mértékben felügyelhető a szabályozási megfelelőségi követelmények teljesítése érdekében. |naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/ApplicationDefinition_Missing_StorageAccount_Deny.json) |
|[Társítások telepítése felügyelt alkalmazásokhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17763ad9-70c0-4794-9397-53d765932634) |Üzembe helyez egy olyan társítási erőforrást, amely a kiválasztott erőforrás-típusokat társítja a megadott felügyelt alkalmazáshoz.  Ez a házirend-telepítés nem támogatja a beágyazott erőforrástípusok használatát. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/AssociationForManagedApplication_Deploy.json) |
