---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0478e606cd5eb6652b992b647d5f69b347753341
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107504271"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure File Sync kapcsolat használata](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d320205-c6a1-4ac6-873d-46224024e8e2) |Ha privát végpontot hoz létre a jelzett Társzinkronizálási szolgáltatás erőforráshoz, akkor a Storage Sync Service-erőforrás címzése nem az internetről elérhető nyilvános végponton, hanem a szervezet hálózatának magánhálózati IP-címterületéből érhető el. A privát végpont önmagában való létrehozása nem tiltja le a nyilvános végpontot. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_AuditIfNotExists.json) |
|[Privát Azure File Sync konfigurálása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb35dddd9-daf7-423b-8375-5a5b86806d5a) |A rendszer egy privát végpontot helyez üzembe a storage sync service erőforráshoz. Ez lehetővé teszi, hogy a Társzinkronizálási szolgáltatás erőforrását a szervezet hálózatának magánhálózati IP-címterületéből címeszi, ne pedig az internetről elérhető nyilvános végponton keresztül. Egy vagy több privát végpont megléte önmagukban nem tiltja le a nyilvános végpontot. |DeployIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_DeployIfNotExists.json) |
|[Módosítás – A Azure File Sync konfigurálása a nyilvános hálózati hozzáférés letiltásához](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e07b2e9-6cd9-4c40-9ccb-52817b95133b) |A Azure File Sync nyilvános végpontját a szervezeti szabályzat letiltja. A Társzinkronizálási szolgáltatás a privát végpont(ak)on keresztül továbbra is elérhető(k). |Módosítás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_IncomingTrafficPolicy_Modify.json) |
|[A nyilvános hálózati hozzáférést le kell tiltani a Azure File Sync](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21a8cd35-125e-4d13-b82d-2e19b7208bb7) |A nyilvános végpont letiltásával korlátozhatja a Storage Sync Service-erőforráshoz való hozzáférést a szervezet hálózatán jóváhagyott privát végpontokra szánt kérelmekre. A kérések nyilvános végpontra való engedélyezése alapvetően nem biztonságos, azonban előfordulhat, hogy le szeretné tiltani, hogy megfeleljen a jogszabályi, jogi vagy szervezeti szabályzatok követelményeinek. A Storage Sync Service nyilvános végpontját úgy tilthatja le, hogy az erőforrás incomingTrafficPolicy házirendet AllowVirtualNetworksOnly beállításra kapcsolja be. |Naplózás, Megtagadás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_IncomingTrafficPolicy_AuditDeny.json) |
