---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 289a2fe7fecc3e28d646e98692c1d5ada4ea7fa8
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106097948"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Storage-fiók célhelyét naplózó SQL-kiszolgálókat 90 napos adatmegőrzéssel vagy magasabb értékkel kell konfigurálni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |Az incidensek vizsgálatához javasoljuk, hogy állítsa be az adatmegőrzést a SQL Server naplózását a Storage-fiók célhelyére legalább 90 napig. Győződjön meg arról, hogy a megfelelő adatmegőrzési szabályok megfelelnek azon régiók számára, amelyekben a rendszert üzemelteti. Ez esetenként szükséges a szabályozási előírásoknak való megfeleléshez. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |
