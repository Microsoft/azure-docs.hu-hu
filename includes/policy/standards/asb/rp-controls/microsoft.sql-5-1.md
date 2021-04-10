---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/24/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d23f4a268322ae65e00b17ed5d83fa555006477d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105105007"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A biztonsági rések felmérését engedélyezni kell a felügyelt SQL-példányon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Minden olyan felügyelt SQL-példány naplózása, amelyen nincs engedélyezve az ismétlődő sebezhetőségi felmérések ellenőrzése. A sebezhetőségi felmérés felderítheti, nyomon követheti és javíthatja a lehetséges adatbázis-réseket. |AuditIfNotExists, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |
|[A sebezhetőségi felmérést engedélyezni kell az SQL-kiszolgálókon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef2a8f2a-b3d9-49cd-a8a8-9a3aaaf647d9) |Naplózza azokat az Azure SQL-kiszolgálókat, amelyeken engedélyezve van az ismétlődő sebezhetőségi felmérések ellenőrzése. A sebezhetőségi felmérés felderítheti, nyomon követheti és javíthatja a lehetséges adatbázis-réseket. |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnServer_Audit.json) |
