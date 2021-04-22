---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 01abec08153dfe900f5509acc8f1f510f92398e0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878409"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Diagnosztikai beállítások üzembe helyezése Data Lake Analytics eseményközpontba](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4daddf25-4823-43d4-88eb-2419eb6dcc08) |Üzembe helyez egy diagnosztikai Data Lake Analytics, hogy egy regionális eseményközpontba streamelhetőek Data Lake Analytics a diagnosztikai beállításokat nem Data Lake Analytics a diagnosztikai beállításokat létrehozták vagy frissítették. |DeployIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DataLakeAnalytics_DeployDiagnosticLog_Deploy_EventHub.json) |
|[Diagnosztikai beállítások üzembe helyezése Data Lake Analytics Log Analytics-munkaterületen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd56a5a7c-72d7-42bc-8ceb-3baf4c0eae03) |Üzembe helyez egy diagnosztikai Data Lake Analytics, hogy egy regionális Log Analytics-munkaterületre streamelhetőek Data Lake Analytics, amelyből hiányzik ez a diagnosztikai beállítás, létrejön vagy frissül. |DeployIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DataLakeAnalytics_DeployDiagnosticLog_Deploy_LogAnalytics.json) |
|[Engedélyezni kell a Data Lake Analytics erőforrásnaplókat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |Naplózza az erőforrásnaplók engedélyezését. Így újból létrehozhatja a vizsgálati célokra használható tevékenységútvonalat; biztonsági incidens vagy a hálózat biztonsága esetén |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |
