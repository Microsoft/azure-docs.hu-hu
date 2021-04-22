---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 613c93ca7f8ad374703b2d021d6cbe8c72a7d7a4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107881140"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Log Analytics-ügynök automatikus építésének engedélyezve kell lennie az előfizetésben](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |A biztonsági rések és fenyegetések figyelése érdekében a Azure Security Center gyűjt adatokat az Azure-beli virtuális gépekről. Az adatokat a Korábban Microsoft Monitoring Agent (MMA) néven ismert Log Analytics-ügynök gyűjti, amely beolvassa a biztonsággal kapcsolatos különböző konfigurációkat és eseménynaplókat a gépről, és elemzés céljából átmásolja az adatokat a Log Analytics-munkaterületre. Javasoljuk, hogy az automatikus kiépítés engedélyezésével automatikusan üzembe helyeződjon az ügynök az összes támogatott Azure-beli és újonnan létrehozott virtuális gépre. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |
|[Azure Monitor naplóprofilnak naplókat kell gyűjtenie az "írás", "törlés" és "művelet" kategóriákhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |Ez a szabályzat biztosítja, hogy a naplóprofil az "írás", "törlés" és "művelet" kategóriák naplóit gyűjtse |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |
|[Azure Monitor kell gyűjtenie a tevékenységnaplókat az összes régióból](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |Ez a szabályzat naplóz Azure Monitor naplóprofilt, amely nem exportálja a tevékenységeket az Összes Azure által támogatott régióból, beleértve a globálist is. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |
