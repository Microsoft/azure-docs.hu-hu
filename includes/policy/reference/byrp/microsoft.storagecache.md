---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: a9295290e8b0a26c407295c3b8f1ccb025159eac
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107504289"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[HPC Cache fiókoknak ügyfél által kezelt kulcsot kell használniuk a titkosításhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F970f84d8-71b6-4091-9979-ace7e3fb6dbb) |A titkosítást a többi Azure HPC Cache ügyfél által kezelt kulcsokkal kezelheti. Alapértelmezés szerint az ügyféladatok szolgáltatás által kezelt kulcsokkal vannak titkosítva, de az ügyfél által kezelt kulcsokra általában szükség van a jogszabályi megfelelőségi szabványoknak való megfeleléshez. Az ügyfél által kezelt kulcsok lehetővé teszik az adatok titkosítását egy olyan Azure Key Vault, amelyet Ön hozott létre és birtokolt. A kulcs életciklusa teljes körű vezérléssel és felelősséggel jár, beleértve a rotációt és a felügyeletet is. |Naplózás, Letiltva, Megtagadás |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageCache_CMKEnabled.json) |
