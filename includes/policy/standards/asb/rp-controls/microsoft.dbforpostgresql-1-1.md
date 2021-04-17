---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 90142fbe2d6feb2a6299a765c563b48388a8fa5f
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107511653"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A privát végpontot engedélyezni kell a PostgreSQL-kiszolgálókhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0564d078-92f5-4f97-8398-b9f58a51f70b) |A privát végpontok kapcsolatai biztonságos kommunikációt kényszerítnek ki azáltal, hogy engedélyezik a Azure Database for PostgreSQL. Konfigurálhat egy privát végpontkapcsolatot, hogy csak az ismert hálózatokról érkező forgalomhoz engedélyezze a hozzáférést, és megakadályozza a hozzáférést minden más IP-címről, beleértve az Azure-ban is. |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnablePrivateEndPoint_Audit.json) |
