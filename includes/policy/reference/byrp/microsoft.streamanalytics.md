---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: be7f7a94953007a6dd583c0049d67ac284d537d6
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873421"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Stream Analytics feladatoknak ügyfél által kezelt kulcsokat kell használniuk az adatok titkosításához](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F87ba29ef-1ab3-4d82-b763-87fcd4f531f7) |Felhasználó által kezelt kulcsokat akkor használjon, ha a tárfiókban szeretné biztonságosan tárolni a Stream Analytics feladatok metaadatait és privát adateszközét. Így teljes hozzáférést biztosít a Stream Analytics titkosításához. |naplózás, tiltás, letiltás |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_CMK_Audit.json) |
|[Diagnosztikai beállítások üzembe helyezése Stream Analytics eseményközpontba](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fedf3780c-3d70-40fe-b17e-ab72013dafca) |Üzembe helyez egy diagnosztikai Stream Analytics, hogy egy regionális eseményközpontba streamelhetőek Stream Analytics a diagnosztikai beállításokat nem Stream Analytics létre vagy frissítsen. |DeployIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/StreamAnalytics_DeployDiagnosticLog_Deploy_EventHub.json) |
|[Diagnosztikai beállítások üzembe helyezése Stream Analytics Log Analytics-munkaterületen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F237e0f7e-b0e8-4ec4-ad46-8c12cb66d673) |Üzembe helyez egy diagnosztikai Stream Analytics, hogy egy regionális Log Analytics-munkaterületre streamelhetőek Stream Analytics a diagnosztikai beállításokat nem Stream Analytics a diagnosztikai beállításokat létrehozó vagy frissített adatokat. |DeployIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/StreamAnalytics_DeployDiagnosticLog_Deploy_LogAnalytics.json) |
|[Engedélyezni kell a Azure Stream Analytics naplóit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |Naplózza az erőforrásnaplók engedélyezését. Ez lehetővé teszi a tevékenységútvonalak újbóli, vizsgálati célokra való használatát; ha biztonsági incidens történik, vagy ha a hálózat biztonsága sérül |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
