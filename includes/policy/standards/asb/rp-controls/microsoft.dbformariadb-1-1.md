---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8ef6a5aaa936c3aa590ce9fb0e5efd939b0035cc
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106097293"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A privát végpontot engedélyezni kell a MariaDB-kiszolgálókon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |A magánhálózati végpontok kapcsolatai biztonságos kommunikációt tesznek lehetővé azáltal, hogy engedélyezik a Azure Database for MariaDBhoz való magánhálózati kapcsolatot. Konfiguráljon egy privát végponti kapcsolatot, amely lehetővé teszi a csak az ismert hálózatokból érkező forgalom elérését, és megakadályozza az összes többi IP-címről való hozzáférést, beleértve az Azure-on belül is. |AuditIfNotExists, letiltva |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
