---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c4129555ef04205d13574be327d0cbb8e2a85ba7
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102613331"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Azure Database for MariaDB lehetővé teszi az adatbázis-kiszolgáló redundancia beállításának kiválasztását. Egy olyan geo-redundáns biztonsági mentési tárolóra állítható be, amelyben az adatai nem csak abban a régióban vannak tárolva, amelyben a kiszolgáló üzemel, de a rendszer egy párosított régióba is replikálja, hogy a helyreállítási lehetőség a régió meghibásodása esetén is biztosítható legyen. A Geo-redundáns tároló a biztonsági mentéshez való konfigurálása csak a kiszolgáló létrehozásakor engedélyezett. |Naplózás, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[A MariaDB-kiszolgálónak virtuális hálózati szolgáltatási végpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdfbd9a64-6114-48de-a47d-90574dc2e489) |A virtuális hálózatokon alapuló tűzfalszabályok lehetővé teszik egy adott alhálózat forgalmának Azure Database for MariaDBét, miközben biztosítják, hogy a forgalom az Azure határán belül maradjon. Ez a házirend lehetővé teszi a naplózást, ha a Azure Database for MariaDB virtuális hálózati szolgáltatásbeli végpontot használ. |AuditIfNotExists, letiltva |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_VirtualNetworkServiceEndpoint_Audit.json) |
|[A privát végpontot engedélyezni kell a MariaDB-kiszolgálókon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |A magánhálózati végpontok kapcsolatai biztonságos kommunikációt tesznek lehetővé azáltal, hogy engedélyezik a Azure Database for MariaDBhoz való magánhálózati kapcsolatot. Konfiguráljon egy privát végponti kapcsolatot, amely lehetővé teszi a csak az ismert hálózatokból érkező forgalom elérését, és megakadályozza az összes többi IP-címről való hozzáférést, beleértve az Azure-on belül is. |AuditIfNotExists, letiltva |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[A MariaDB-kiszolgálók esetében le kell tiltani a nyilvános hálózati hozzáférést](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |Tiltsa le a nyilvános hálózati hozzáférés tulajdonságot a biztonság növelése érdekében, és gondoskodjon arról, hogy a Azure Database for MariaDB csak privát végpontról legyen elérhető. Ez a konfiguráció szigorúan letiltja az Azure IP-címtartományen kívüli nyilvános címtartomány elérését, és megtagadja az IP-vagy virtuális hálózatokra vonatkozó tűzfalszabályok egyeztetését. |Naplózás, letiltva |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json) |
