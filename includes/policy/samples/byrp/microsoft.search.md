---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/29/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 3dabcb0b27a19b275556135f73a0c504a8ad1648
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/31/2020
ms.locfileid: "84234857"
---
|Name (Név) |Leírás |Hatás (ok) |Verzió |GitHub |
|---|---|---|---|---|
|[A keresési szolgáltatások diagnosztikai beállításainak üzembe helyezése az Event hub-ban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3d5da587-71bd-41f5-ac95-dd3330c2d58d) |A keresési szolgáltatások diagnosztikai beállításainak üzembe helyezése a regionális esemény központba, ha bármely olyan keresési szolgáltatás, amelyből hiányzik ez a diagnosztikai beállítás, létrejön vagy frissül. |DeployIfNotExists, letiltva |2.0.0 |[Hivatkozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Search_DeployDiagnosticLog_Deploy_EventHub.json) |
|[A keresési szolgáltatások diagnosztikai beállításainak üzembe helyezése Log Analytics munkaterületre](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08ba64b8-738f-4918-9686-730d2ed79c7d) |Központilag telepíti a keresési szolgáltatások diagnosztikai beállításait egy regionális Log Analytics munkaterületre, amikor bármely olyan keresési szolgáltatást létrehoz vagy frissít, amelyből hiányzik ez a diagnosztikai beállítás. |DeployIfNotExists, letiltva |1.0.0 |[Hivatkozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Search_DeployDiagnosticLog_Deploy_LogAnalytics.json) |
|[A keresési szolgáltatásokban engedélyezni kell a diagnosztikai naplókat.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |Diagnosztikai naplók engedélyezésének naplózása. Ez lehetővé teszi, hogy újból létrehozza a vizsgálati célokra használandó tevékenység-nyomvonalat; biztonsági incidens bekövetkezésekor vagy a hálózat biztonsága esetén |AuditIfNotExists, letiltva |2.0.0 |[Hivatkozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
