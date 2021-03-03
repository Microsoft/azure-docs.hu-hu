---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8a96ca2bfa99cbf8cb8f06de9883f5281496cf93
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2021
ms.locfileid: "101725646"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Monitor log-profilnak össze kell gyűjtenie a naplókat a "Write", "Delete" és "Action" kategóriákba.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |Ez a szabályzat biztosítja, hogy a log-profil a "Write", a "Delete" és a "Action" kategóriákba gyűjti a naplókat. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |
|[Azure Monitor az összes régióból gyűjti a tevékenység naplóit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |Ez a házirend naplózza a Azure Monitor log profilt, amely nem exportál tevékenységeket az összes Azure által támogatott régióból, beleértve a globálisat is. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |
