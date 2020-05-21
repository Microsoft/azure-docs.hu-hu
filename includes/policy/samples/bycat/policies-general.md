---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/13/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 98dd4fe10cf49cfbe27d04ee713c6194a0bb636b
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83651481"
---
|Name |Leírás |Hatás (ok) |Verzió |GitHub |
|---|---|---|---|---|
|[Engedélyezett helyek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe56962a6-4747-49cd-b67b-bf8b01975c4c) |Ezzel a szabályzattal korlátozható azon helyek köre, amelyeket a szervezet megadhat az erőforrások üzembe helyezésekor. A földrajzi megfelelőségi követelmények betartására szolgál. Az erőforráscsoportok, a Microsoft. AzureActiveDirectory/b2cDirectories és a "globális" régiót használó erőforrások kizárása. |tagadja |1.0.0 |[Hivatkozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/AllowedLocations_Deny.json) |
|[Erőforráscsoportok engedélyezett helyei](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe765b5de-1225-4ba3-bd56-1ac6695af988) |Ez a szabályzat lehetővé teszi, hogy korlátozza a szervezete által az erőforráscsoportok létrehozásához szükséges helyet. A földrajzi megfelelőségi követelmények betartására szolgál. |tagadja |1.0.0 |[Hivatkozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/ResourceGroupAllowedLocations_Deny.json) |
|[Engedélyezett erőforrástípusok](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa08ec900-254a-4555-9bf5-e42af04b5c5c) |Ez a szabályzat lehetővé teszi a szervezet által üzembe helyezhető erőforrástípusok megadását. Ez a szabályzat csak a "címkéket" és a "location" támogatást támogató erőforrásokat fogja érinteni. Az összes erőforrás korlátozásához ismételje meg ezt a házirendet, és módosítsa a "Mode" beállítást az "all" értékre. |tagadja |1.0.0 |[Hivatkozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/AllowedResourceTypes_Deny.json) |
|[A naplózási erőforrás helye megegyezik az erőforráscsoport helyével](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a914e76-4921-4c19-b460-a2d36003525a) |Annak naplózása, hogy az erőforrás helye megegyezik az erőforráscsoport helyével |ellenőrzési |2.0.0 |[Hivatkozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/ResourcesInResourceGroupLocation_Audit.json) |
|[Egyéni RBAC-szabályok használatának naplózása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |Olyan beépített szerepköröket naplózhat, mint például a "tulajdonos, közreműködő, olvasó", az egyéni RBAC-szerepkörök helyett, amelyek hajlamosak a hibákra. Az egyéni szerepkörök használata Kivételként kezelendő, és szigorú felülvizsgálati és veszélyforrás-modellezést igényel |Naplózás, letiltva |1.0.0 |[Hivatkozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[Az egyéni előfizetés tulajdonosi szerepkörei nem létezhetnek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Ez a szabályzat biztosítja, hogy egyetlen egyéni előfizetés-tulajdonosi szerepkör sem létezik. |Naplózás, letiltva |1.0.0 |[Hivatkozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |
|[Nem engedélyezett erőforrástípusok](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6c112d4e-5bc7-47ae-a041-ea2d9dccd749) |Ez a szabályzat lehetővé teszi azon erőforrástípusok megadását, amelyeket a szervezet nem tud központilag telepíteni. |Megtagadás |1.0.0 |[Hivatkozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/InvalidResourceTypes_Deny.json) |
