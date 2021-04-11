---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/24/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 4b3939094b4299ccf75e4953443f8ce9d0075c59
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105031697"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure File Sync a magánhálózati hivatkozást kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d320205-c6a1-4ac6-873d-46224024e8e2) |A megadott Storage Sync Service-erőforráshoz tartozó magánhálózati végpont létrehozása lehetővé teszi a Storage-szinkronizálási szolgáltatás erőforrásának a szervezet hálózatának magánhálózati IP-címén belüli, nem pedig az internetről elérhető nyilvános végponton keresztüli elérését. A privát végpontok önmagukban történő létrehozása nem tiltja le a nyilvános végpontot. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_AuditIfNotExists.json) |
|[Azure File Sync konfigurálása magánhálózati végpontokkal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb35dddd9-daf7-423b-8375-5a5b86806d5a) |A rendszer egy privát végpontot telepít a jelzett Storage Sync Service-erőforráshoz. Ez lehetővé teszi, hogy a Storage Sync Service-erőforrást a szervezete hálózatának magánhálózati IP-címén belül, az internetről elérhető nyilvános végponton keresztül foglalkozzon. Egy vagy több magán-végpont létezése önmagában nem tiltja le a nyilvános végpontot. |DeployIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_DeployIfNotExists.json) |
|[Módosítás – Azure File Sync konfigurálása a nyilvános hálózati hozzáférés letiltásához](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e07b2e9-6cd9-4c40-9ccb-52817b95133b) |A szervezeti házirend tiltja le a Azure File Sync internetről elérhető nyilvános végpontját. Továbbra is hozzáférhet a Storage Sync szolgáltatáshoz saját privát végpontja (ke) n keresztül. |Módosítás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_IncomingTrafficPolicy_Modify.json) |
|[A nyilvános hálózati hozzáférést le kell tiltani Azure File Sync](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21a8cd35-125e-4d13-b82d-2e19b7208bb7) |A nyilvános végpont letiltása lehetővé teszi, hogy korlátozza a Storage Sync Service-erőforráshoz való hozzáférést a szervezet hálózatán jóváhagyott privát végpontokra irányuló kérelmekre. A nyilvános végpontra irányuló kérések engedélyezése nem jelent semmit, mivel előfordulhat, hogy le kívánja tiltani a szabályozási, jogi vagy szervezeti házirend követelményeinek teljesítése érdekében. A Storage Sync szolgáltatás nyilvános végpontjának letiltásához állítsa az erőforrás incomingTrafficPolicy az AllowVirtualNetworksOnly értékre. |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_IncomingTrafficPolicy_AuditDeny.json) |
