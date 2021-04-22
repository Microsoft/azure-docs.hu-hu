---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f14136e77bb0018ea5e15f6e07fa7a633addf49f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876017"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[HPC Cache fiókoknak ügyfél által felügyelt kulcsot kell használniuk a titkosításhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F970f84d8-71b6-4091-9979-ace7e3fb6dbb) |A titkosítás kezelése a Azure HPC Cache felügyelt kulcsokkal. Alapértelmezés szerint az ügyféladatok szolgáltatás által kezelt kulcsokkal vannak titkosítva, de az ügyfél által kezelt kulcsokra általában szükség van a jogszabályi megfelelőségi szabványoknak való megfeleléshez. Az ügyfél által kezelt kulcsok lehetővé teszik az adatok titkosítását egy olyan Azure Key Vault, amelyet Ön hozott létre és birtokolt. A kulcs életciklusa teljes körű vezérléssel és felelősséggel jár, beleértve a rotációt és a felügyeletet is. |Naplózás, Letiltva, Megtagadás |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageCache_CMKEnabled.json) |
