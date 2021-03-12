---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 60ddf7a90f478fa0b286f14a1cba4d609b02e321
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020939"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az SQL Server-kiszolgálóknak legalább 90 napig meg kell őrizniük a naplózási adatgyűjtést](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |Az incidensek vizsgálatához javasoljuk, hogy az SQL Server-kiszolgálók naplózási adataihoz legalább 90 napig állítsa be az adatmegőrzést. Győződjön meg arról, hogy a megfelelő adatmegőrzési szabályok megfelelnek azon régiók számára, amelyekben a rendszert futtatja. Ez esetenként szükséges a szabályozási előírásoknak való megfeleléshez. |AuditIfNotExists, letiltva |[2.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |
