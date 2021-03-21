---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 564c07c3a59e13dda5fbbc992855d5ca6019dad6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586810"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az SQL Server-kiszolgálóknak legalább 90 napig meg kell őrizniük a naplózási adatgyűjtést](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |Az incidensek vizsgálatához javasoljuk, hogy az SQL Server-kiszolgálók naplózási adataihoz legalább 90 napig állítsa be az adatmegőrzést. Győződjön meg arról, hogy a megfelelő adatmegőrzési szabályok megfelelnek azon régiók számára, amelyekben a rendszert futtatja. Ez esetenként szükséges a szabályozási előírásoknak való megfeleléshez. |AuditIfNotExists, letiltva |[2.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |
