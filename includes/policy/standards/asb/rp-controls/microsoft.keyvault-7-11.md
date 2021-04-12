---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f3d63180219caa99fed22a5f8249de7e8f1a3eea
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106096901"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A kulcstárolóknak engedélyezve kell lennie a kiürítési védelemmel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |A kulcstartó rosszindulatú törlése maradandó adatvesztést eredményezhet. A szervezet rosszindulatú bennfentes a kulcstartók törlésére és kiürítésére is képes. A védelem törlésével megvédheti a bennfentes támadásoktól a nem kötelező megőrzési idő kikényszerítésével a törölt kulcstartók esetében. A szervezeten belül a Microsoft nem tudja törölni a kulcstárolókat a helyreállítható törlés megőrzési időszak alatt. |Naplózás, megtagadás, letiltva |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |
