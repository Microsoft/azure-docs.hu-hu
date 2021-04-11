---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c8943d7b02a6d6d6d8c5957166a4c12302ae69bc
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106091121"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Logic Apps integrációs szolgáltatási környezet titkosítása az ügyfél által felügyelt kulcsokkal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1fafeaf6-7927-4059-a50a-8eb2a7a6f2b5) |Az ügyfél által felügyelt kulcsok használatával integrációs szolgáltatási környezet üzembe helyezésével kezelheti az Logic Apps adatok titkosítását. Alapértelmezés szerint a rendszer a szolgáltatás által felügyelt kulcsokkal titkosítja az ügyféladatokat, az ügyfél által felügyelt kulcsokat azonban általában a szabályozási megfelelőségi előírások teljesítéséhez szükséges. Az ügyfél által felügyelt kulcsok lehetővé teszik az adattitkosítást az Ön által létrehozott és birtokolt Azure Key Vault kulccsal. A kulcs életciklusának teljes körű ellenőrzése és felelőssége, beleértve a rotációt és a felügyeletet is. |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_ISEWithCustomerManagedKey_AuditDeny.json) |
|[A Logic Apps üzembe helyezése integrációs szolgáltatási környezet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdc595cb1-1cde-45f6-8faf-f88874e1c0e1) |Logic Apps üzembe helyezése a virtuális hálózatban integrációs szolgáltatási környezet a speciális Logic Apps hálózatkezelési és biztonsági funkciók, valamint a hálózati konfiguráció nagyobb mértékű szabályozása. További információ: [https://aka.ms/integration-service-environment](https://aka.ms/integration-service-environment) . A integrációs szolgáltatási környezet üzembe helyezése lehetővé teszi a titkosítást az ügyfél által felügyelt kulcsokkal, amelyek fokozott adatvédelmet biztosítanak a titkosítási kulcsok kezelésének lehetővé tételével. Ez gyakran megfelel a megfelelőségi követelményeknek. |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_LogicAppsInISE_AuditDeny.json) |
|[A Logic Apps erőforrás-naplóit engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |Az erőforrás-naplók engedélyezésének naplózása. Ez lehetővé teszi, hogy újból létrehozza a vizsgálati célokra használandó tevékenység-nyomvonalat; biztonsági incidens bekövetkezésekor vagy a hálózat biztonsága esetén |AuditIfNotExists, letiltva |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |
