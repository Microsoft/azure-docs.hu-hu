---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 3a9221b40f01d8b01f9b343865f4e9d0276aea9c
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498729"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Logic Apps integrációs szolgáltatási környezet felhasználó által kezelt kulcsokkal kell titkosítani](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1fafeaf6-7927-4059-a50a-8eb2a7a6f2b5) |Üzembe helyezés integrációs szolgáltatási környezet a titkosítás kezeléséhez a többi Logic Apps ügyfél által kezelt kulcsokkal. Alapértelmezés szerint az ügyféladatok szolgáltatás által kezelt kulcsokkal vannak titkosítva, de az ügyfél által kezelt kulcsokra általában szükség van a jogszabályi megfelelőségi szabványoknak való megfeleléshez. Az ügyfél által kezelt kulcsok lehetővé teszik az adatok titkosítását egy olyan Azure Key Vault, amelyet Ön hozott létre és birtokolt. A kulcs életciklusa teljes körű vezérléssel és felelősséggel jár, beleértve a rotációt és a felügyeletet is. |Naplózás, Megtagadás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_ISEWithCustomerManagedKey_AuditDeny.json) |
|[Logic Apps üzembe kell helyezni a integrációs szolgáltatási környezet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdc595cb1-1cde-45f6-8faf-f88874e1c0e1) |A Logic Apps virtuális integrációs szolgáltatási környezet való üzembe helyezésével fejlett hálózati és Logic Apps szolgáltatásokat biztosít, és nagyobb fokú irányítást biztosít a hálózati konfiguráció felett. További információ: [https://aka.ms/integration-service-environment](https://aka.ms/integration-service-environment) . A integrációs szolgáltatási környezet lehetővé teszi az ügyfél által kezelt kulcsokkal történő titkosítást is, ami fokozott adatvédelmet biztosít a titkosítási kulcsok kezeléséhez. Ez gyakran azért van, hogy megfeleljen a megfelelőségi követelményeknek. |Naplózás, Megtagadás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_LogicAppsInISE_AuditDeny.json) |
|[Engedélyezni kell a Logic Apps naplóit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |Naplózza az erőforrásnaplók engedélyezését. Ez lehetővé teszi a tevékenységútvonalak újbóli, vizsgálati célokra való használatát; ha biztonsági incidens történik, vagy ha a hálózat biztonsága sérül |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |
