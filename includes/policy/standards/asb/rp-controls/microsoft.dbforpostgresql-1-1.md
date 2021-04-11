---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c3bc47befc71881ac2e53a0d2999fd881b97a9d3
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "107284326"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A privát végpontot engedélyezni kell a PostgreSQL-kiszolgálókhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0564d078-92f5-4f97-8398-b9f58a51f70b) |A magánhálózati végpontok kapcsolatai biztonságos kommunikációt tesznek lehetővé azáltal, hogy engedélyezik a Azure Database for PostgreSQLhoz való magánhálózati kapcsolatot. Konfiguráljon egy privát végponti kapcsolatot, amely lehetővé teszi a csak az ismert hálózatokból érkező forgalom elérését, és megakadályozza az összes többi IP-címről való hozzáférést, beleértve az Azure-on belül is. |AuditIfNotExists, letiltva |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnablePrivateEndPoint_Audit.json) |
