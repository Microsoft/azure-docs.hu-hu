---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8ca15686659a7fb9bd1390df7d9af041805df7ee
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106091385"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az Azure Container instance Container Group üzembe helyezése virtuális hálózatban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8af8f826-edcb-4178-b35f-851ea6fea615) |Biztonságos kommunikáció a tárolók között az Azure Virtual Networks szolgáltatással. Amikor megad egy virtuális hálózatot, a virtuális hálózaton belüli erőforrások biztonságosan és magánjellegű módon kommunikálhatnak egymással. |Naplózás, letiltva, megtagadás |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Instance/ContainerInstance_VNET_Audit.json) |
|[Az Azure Container instance Container csoportnak az ügyfél által felügyelt kulcsot kell használnia a titkosításhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0aa61e00-0a01-4a3c-9945-e93cffedf0e6) |Az ügyfél által felügyelt kulcsok használatával nagyobb rugalmasságot biztosíthat a tárolók számára. Felhasználó által kezelt kulcs megadásakor a megadott kulccsal védi és szabályozza az adatokat titkosító kulcs hozzáférését. Az ügyfél által felügyelt kulcsok használata további képességeket biztosít a kulcs titkosítási kulcsainak elforgatásának vezérléséhez vagy az adattitkosítás törléséhez. |Naplózás, letiltva, megtagadás |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Instance/ContainerInstance_CMK_Audit.json) |
