---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/04/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b448fbb9a7d382a785fd66c0edb197499c8c5c79
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99561183"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az Azure-beli adatüzemeket ügyfél által felügyelt kulccsal kell titkosítani (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ec52d6d-beb7-40c4-9a9e-fe753254690e) |Az ügyfél által felügyelt kulcsok (CMKs-EK) segítségével kezelheti a titkosítást a Azure Data Factory többi részén. Alapértelmezés szerint a rendszer a szolgáltatás által felügyelt kulcsokkal titkosítja az ügyféladatokat, de a CMKs általában a szabályozási megfelelőségi előírások teljesítéséhez szükségesek. A CMKs lehetővé teszi az adattitkosítást egy Ön által létrehozott és birtokolt Azure Key Vault kulccsal. A kulcs életciklusának teljes körű ellenőrzése és felelőssége, beleértve a rotációt és a felügyeletet is. További információ a CMK-titkosításról: [https://aka.ms/adf-cmk](https://aka.ms/adf-cmk) . |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/DataFactory_CustomerManagedKey_Audit.json) |
|[A Azure Data Factory nyilvános hálózati hozzáférését le kell tiltani](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1cf164be-6819-4a50-b8fa-4bcaa4f98fb6) |A nyilvános hálózati hozzáférési tulajdonság letiltása növeli a biztonságot azáltal, hogy a Azure Data Factory csak privát végpontról érhető el. |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/DataFactory_PublicNetworkAccess_Audit.json) |
