---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 9a2c461a801f78139ca0bb255ccc9462fbd75d49
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2020
ms.locfileid: "90022632"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A keresési szolgáltatások diagnosztikai beállításainak üzembe helyezése az Event hub-ban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3d5da587-71bd-41f5-ac95-dd3330c2d58d) |A keresési szolgáltatások diagnosztikai beállításainak üzembe helyezése a regionális esemény központba, ha bármely olyan keresési szolgáltatás, amelyből hiányzik ez a diagnosztikai beállítás, létrejön vagy frissül. |DeployIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Search_DeployDiagnosticLog_Deploy_EventHub.json) |
|[A keresési szolgáltatások diagnosztikai beállításainak üzembe helyezése Log Analytics munkaterületre](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08ba64b8-738f-4918-9686-730d2ed79c7d) |Központilag telepíti a keresési szolgáltatások diagnosztikai beállításait egy regionális Log Analytics munkaterületre, amikor bármely olyan keresési szolgáltatást létrehoz vagy frissít, amelyből hiányzik ez a diagnosztikai beállítás. |DeployIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Search_DeployDiagnosticLog_Deploy_LogAnalytics.json) |
|[A keresési szolgáltatásokban engedélyezni kell a diagnosztikai naplókat.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |Diagnosztikai naplók engedélyezésének naplózása. Ez lehetővé teszi, hogy újból létrehozza a vizsgálati célokra használandó tevékenység-nyomvonalat; biztonsági incidens bekövetkezésekor vagy a hálózat biztonsága esetén |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
