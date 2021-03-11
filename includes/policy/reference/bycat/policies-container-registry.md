---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 553627e0ae32aff698ac2d38f8eb22e0191739ac
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102610839"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A tároló-beállításjegyzékek titkosítása ügyfél által felügyelt kulccsal történik](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Az ügyfél által felügyelt kulcsok használatával kezelheti a beállításjegyzékben lévő adatok titkosítását. Alapértelmezés szerint az adatok titkosítva vannak a szolgáltatás által felügyelt kulcsokkal, de az ügyfél által felügyelt kulcsokat általában a szabályozási megfelelőségi előírások teljesítéséhez kell végrehajtani. Az ügyfél által felügyelt kulcsok lehetővé teszik az adattitkosítást az Ön által létrehozott és birtokolt Azure Key Vault kulccsal. A kulcs életciklusának teljes körű ellenőrzése és felelőssége, beleértve a rotációt és a felügyeletet is. További információ: [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK) . |Naplózás, megtagadás, letiltva |[1.1.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[A tároló-beállításjegyzékek nem engedélyezhetik a nem korlátozott hálózati hozzáférést](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Az Azure-beli tároló-beállításjegyzékek alapértelmezés szerint a hálózatról származó gazdagépekről fogadják az interneten keresztüli kapcsolatokat. Ha az esetleges fenyegetésekkel szemben kívánja megvédeni a beállításjegyzékét, csak bizonyos nyilvános IP-címekről vagy címtartományok elérését engedélyezze. Ha a beállításjegyzék nem rendelkezik IP-/tűzfalszabály-vagy konfigurált virtuális hálózattal, akkor az nem megfelelő állapotú erőforrásokban fog megjelenni. További információ a Container Registry hálózati szabályokról: [https://aka.ms/acr/portal/public-network](https://aka.ms/acr/portal/public-network) és itt [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet) . |Naplózás, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[A tároló-beállításjegyzéknek privát hivatkozást kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Az Azure Private link használatával a virtuális hálózatot az Azure-szolgáltatásokhoz nyilvános IP-cím nélkül csatlakoztathatja a forráson vagy a célhelyen. A privát kapcsolati platform kezeli a fogyasztó és a szolgáltatások közötti kapcsolatot az Azure gerinc hálózaton. Ha privát végpontokat rendel a tároló-beállításjegyzékhez a teljes szolgáltatás helyett, akkor az adatszivárgási kockázatokkal szemben is védve lesz. További információ: [https://aka.ms/acr/private-link](https://aka.ms/acr/private-link) . |Naplózás, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
