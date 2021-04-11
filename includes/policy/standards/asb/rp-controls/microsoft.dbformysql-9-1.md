---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 175c630e8fc3959844e11def5f82475ac1d4fb26
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106097172"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |Azure Database for MySQL lehetővé teszi az adatbázis-kiszolgáló redundancia beállításának kiválasztását. Egy olyan geo-redundáns biztonsági mentési tárolóra állítható be, amelyben az adatai nem csak abban a régióban vannak tárolva, amelyben a kiszolgáló üzemel, de a rendszer egy párosított régióba is replikálja, hogy a helyreállítási lehetőség a régió meghibásodása esetén is biztosítható legyen. A Geo-redundáns tároló a biztonsági mentéshez való konfigurálása csak a kiszolgáló létrehozásakor engedélyezett. |Naplózás, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |
