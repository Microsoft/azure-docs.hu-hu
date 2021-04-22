---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 5d0f73d4991f4f14460e569cfcae29722e77cd1e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107880761"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A tárfiókok korlátozzák a hálózati hozzáférést](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |Korlátozni kell a tárfiókok hálózati hozzáférését. Konfigurálja úgy a hálózati szabályokat, hogy csak az engedélyezett hálózatokról származó alkalmazások férnek hozzá a tárfiókhoz. Az adott internetről vagy helyszíni ügyfelekről származó kapcsolatok lehetővé teszik a hozzáférést adott Azure-beli virtuális hálózatokról származó forgalomhoz vagy a nyilvános internetes IP-címtartományok felé |Naplózás, Megtagadás, Letiltva |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|[A tárfiókok virtuális hálózati szolgáltatásvégpontot használjanak](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Ez a szabályzat naplót végez minden olyan storage-fiókot, amely nincs virtuális hálózati szolgáltatásvégpont használatára konfigurálva. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
