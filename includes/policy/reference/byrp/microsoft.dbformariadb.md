---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d2e6eb6f38ed85a6b943653ed3e1464ebe629799
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873080"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A georedundáns biztonsági mentést engedélyezni kell a Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Azure Database for MariaDB az adatbázis-kiszolgáló redundancia-beállításának kiválasztása. Beállítható georedundáns biztonsági mentési tárra, amelyben az adatok nem csak a kiszolgálót tároló régióban tárolódnak, hanem egy párosított régióba is replikálhatók, hogy régióhiba esetén helyreállítási lehetőséget biztosítson. A georedundáns tárolás biztonsági mentéshez való konfigurálása csak a kiszolgáló létrehozása során engedélyezett. |Naplózás, Letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[A MariaDB-kiszolgálónak virtuális hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdfbd9a64-6114-48de-a47d-90574dc2e489) |A virtuális hálózatra vonatkozó tűzfalszabályok segítségével engedélyezhető az adott alhálózatról a Azure Database for MariaDB közötti forgalom, miközben gondoskodik arról, hogy a forgalom az Azure-határon belül maradjon. Ez a szabályzat lehetővé teszi annak naplózását, hogy a Azure Database for MariaDB virtuális hálózati szolgáltatásvégpontot használ-e. |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_VirtualNetworkServiceEndpoint_Audit.json) |
|[A privát végpontot engedélyezni kell a MariaDB-kiszolgálókhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |A privát végponti kapcsolatok a biztonságos kommunikációt kényszerítik ki azáltal, hogy engedélyezik a Azure Database for MariaDB. Konfigurálhat egy privát végpontkapcsolatot, hogy csak az ismert hálózatokról érkező forgalomhoz engedélyezze a hozzáférést, és megakadályozza a hozzáférést minden más IP-címről, beleértve az Azure-t is. |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[Le kell tiltani a nyilvános hálózati hozzáférést a MariaDB-kiszolgálókhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |Tiltsa le a nyilvános hálózati hozzáférés tulajdonságot a biztonság növelése érdekében, és Azure Database for MariaDB, hogy az adatok csak privát végpontról érhetők el. Ez a konfiguráció szigorúan letiltja az Azure IP-címtartományán kívüli nyilvános címtartományok hozzáférését, és letilt minden olyan bejelentkezést, amely megfelel az IP- vagy virtuális hálózatalapú tűzfalszabályoknak. |Naplózás, Letiltva |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json) |
