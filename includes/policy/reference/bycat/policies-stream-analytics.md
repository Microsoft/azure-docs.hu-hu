---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 27df257c6445c89ba7a02a5477841d8e67da4730
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102611315"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az adatok titkosításához Azure Stream Analytics feladatokhoz az ügyfél által felügyelt kulcsokat kell használni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F87ba29ef-1ab3-4d82-b763-87fcd4f531f7) |Használja az ügyfél által felügyelt kulcsokat, ha biztonságosan szeretné tárolni a Stream Analytics feladatok metaadatait és személyes adategységeit a Storage-fiókban. Ezzel teljes körűen vezérelheti a Stream Analytics-adat titkosítását. |naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_CMK_Audit.json) |
|[A Azure Stream Analytics erőforrás-naplóit engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |Az erőforrás-naplók engedélyezésének naplózása. Ez lehetővé teszi, hogy újból létrehozza a vizsgálati célokra használandó tevékenység-nyomvonalat; biztonsági incidens bekövetkezésekor vagy a hálózat biztonsága esetén |AuditIfNotExists, letiltva |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
