---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 1f8d96797935a72ffb80787463f571fa5b4b6540
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106097452"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A magánhálózati végpontot engedélyezni kell a MySQL-kiszolgálókon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7595c971-233d-4bcf-bd18-596129188c49) |A magánhálózati végpontok kapcsolatai biztonságos kommunikációt tesznek lehetővé azáltal, hogy engedélyezik a Azure Database for MySQLhoz való magánhálózati kapcsolatot. Konfiguráljon egy privát végponti kapcsolatot, amely lehetővé teszi a csak az ismert hálózatokból érkező forgalom elérését, és megakadályozza az összes többi IP-címről való hozzáférést, beleértve az Azure-on belül is. |AuditIfNotExists, letiltva |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnablePrivateEndPoint_Audit.json) |
