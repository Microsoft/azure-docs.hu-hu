---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 64903629909b747918094d517ec48f06e029f520
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107879750"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A privát végpontot engedélyezni kell a MariaDB-kiszolgálókhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |A privát végponti kapcsolatok biztonságos kommunikációt kényszerítnek ki azáltal, hogy engedélyezik a Azure Database for MariaDB. Konfigurálhat egy privát végpontkapcsolatot, hogy csak az ismert hálózatokról érkező forgalomhoz engedélyezze a hozzáférést, és megakadályozza a hozzáférést minden más IP-címről, beleértve az Azure-ban is. |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
