---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/03/2020
ms.author: dacoulte
ms.openlocfilehash: 4ed4dbf91a487d5a1a4a077f1b4031fb208463b3
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758641"
---
|Név |Leírás |Hatás(ok) |Verzió |GitHub |
|---|---|---|---|---|
|[Az alkalmazáskonfigurációnak ügyfél által felügyelt kulcsot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F967a4b4b-2da9-43c1-b7d0-f98d0d74d0b1) |Ez a házirend minden olyan alkalmazáskonfigurációs példányt naplóz, amely nem használ ügyfél által kezelt kulcsot. |Naplózás, letiltva |1.0.0 |[Hivatkozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/CustomerManagedKey_Audit.json)
|[Az alkalmazáskonfigurációnak privát hivatkozást kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca610c1d-041c-4332-9d88-7ed3094967c7) |Ez a házirend minden olyan alkalmazáskonfigurációs példányt naplóz, amely nem használ privát hivatkozást. |AuditIfNotExists, Letiltva |1.0.0 |[Hivatkozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/PrivateLink_Audit.json)
