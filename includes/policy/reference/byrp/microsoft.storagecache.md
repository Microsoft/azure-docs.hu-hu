---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 9d4462e49cac4af564df8ff9d7962acf7512a7f6
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106095544"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A HPC-gyorsítótár fiókjainak az ügyfél által felügyelt kulcsot kell használniuk a titkosításhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F970f84d8-71b6-4091-9979-ace7e3fb6dbb) |Kezelje az Azure HPC cache-ben lévő titkosítást az ügyfél által felügyelt kulcsokkal. Alapértelmezés szerint a rendszer a szolgáltatás által felügyelt kulcsokkal titkosítja az ügyféladatokat, az ügyfél által felügyelt kulcsokat azonban általában a szabályozási megfelelőségi előírások teljesítéséhez szükséges. Az ügyfél által felügyelt kulcsok lehetővé teszik az adattitkosítást az Ön által létrehozott és birtokolt Azure Key Vault kulccsal. A kulcs életciklusának teljes körű ellenőrzése és felelőssége, beleértve a rotációt és a felügyeletet is. |Naplózás, letiltva, megtagadás |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageCache_CMKEnabled.json) |
