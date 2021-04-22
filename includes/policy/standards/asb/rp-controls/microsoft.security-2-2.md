---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: e6cdbadd01e93a172389bf67474d0736bc2a7687
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107880512"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Log Analytics-ügynök automatikus építésének engedélyezve kell lennie az előfizetésben](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |A biztonsági rések és fenyegetések figyelése érdekében a Azure Security Center gyűjt adatokat az Azure-beli virtuális gépekről. Az adatokat a Korábban Microsoft Monitoring Agent (MMA) néven ismert Log Analytics-ügynök gyűjti, amely beolvassa a biztonsággal kapcsolatos különböző konfigurációkat és eseménynaplókat a gépről, és elemzés céljából átmásolja az adatokat a Log Analytics-munkaterületre. Javasoljuk, hogy az automatikus kiépítés engedélyezésével automatikusan üzembe helyeződjon az ügynök az összes támogatott Azure-beli és újonnan létrehozott virtuális gépre. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |
