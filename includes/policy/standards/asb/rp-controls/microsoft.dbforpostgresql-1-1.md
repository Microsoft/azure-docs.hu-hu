---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0f48d14174a880d0de0072cb39cc6853ced1db03
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107880764"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A privát végpontot engedélyezni kell a PostgreSQL-kiszolgálókhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0564d078-92f5-4f97-8398-b9f58a51f70b) |A privát végponti kapcsolatok a biztonságos kommunikációt kényszerítik ki azáltal, hogy engedélyezik a Azure Database for PostgreSQL. Konfigurálhat egy privát végpontkapcsolatot, hogy csak az ismert hálózatokról érkező forgalomhoz engedélyezze a hozzáférést, és megakadályozza a hozzáférést minden más IP-címről, beleértve az Azure-t is. |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnablePrivateEndPoint_Audit.json) |
