---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b9720165ce5ff27e1e67e074afa64d21a78efaa6
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498674"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az Azure Container Instance-tárolócsoportot virtuális hálózaton kell üzembe helyezni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8af8f826-edcb-4178-b35f-851ea6fea615) |Biztonságos kommunikáció a tárolók között az Azure Virtual Networks segítségével. Virtuális hálózat megadásakor a virtuális hálózaton belüli erőforrások biztonságosan és privát módon kommunikálhatnak egymással. |Naplózás, Letiltva, Megtagadás |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Instance/ContainerInstance_VNET_Audit.json) |
|[Az Azure Container Instance-tárolócsoportnak ügyfél által felügyelt kulcsot kell használnia a titkosításhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0aa61e00-0a01-4a3c-9945-e93cffedf0e6) |A tárolókat nagyobb rugalmassággal biztosíthatja az ügyfél által kezelt kulcsokkal. Felhasználó által kezelt kulcs megadásakor a megadott kulccsal védi és szabályozza az adatokat titkosító kulcs hozzáférését. Az ügyfél által kezelt kulcsok használata további képességeket biztosít a kulcstitkosítási kulcs rotációja vagy az adatok titkosítással való törlésének szabályozására. |Naplózás, Letiltva, Megtagadás |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Instance/ContainerInstance_CMK_Audit.json) |
