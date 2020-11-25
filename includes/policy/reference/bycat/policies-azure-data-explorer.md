---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 9024d12259a45dcf4e8321866f5c50d01c888934
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2020
ms.locfileid: "96007801"
---
|Név<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az Azure Adatkezelő a REST-alapú titkosításhoz ügyfél által felügyelt kulcsot kell használni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81e74cea-30fd-40d5-802f-d72103c2aaaa) |A titkosítás inaktív állapotban való engedélyezése az Azure Adatkezelő-fürtön lévő ügyfél által felügyelt kulcs segítségével további vezérlést biztosít a titkosítás által használt kulcshoz. Ez a funkció gyakran alkalmazható a különleges megfelelőségi követelményeknek megfelelő ügyfelekre, és a kulcsok kezeléséhez Key Vault szükséges. |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_CMK.json) |
|[A lemezes titkosítást engedélyezni kell az Azure Adatkezelő](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4b53539-8df9-40e4-86c6-6b607703bd4e) |A lemez titkosításának engedélyezése segíti az adatai védelmét és védelmét a szervezeti biztonsági és megfelelőségi kötelezettségvállalások teljesítése érdekében. |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_disk_encrypted.json) |
|[A kettős titkosítást engedélyezni kell az Azure Adatkezelő](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fec068d99-e9c7-401f-8cef-5bdde4e6ccf1) |A kettős titkosítás engedélyezése segíti az adatai védelmét és védelmét a szervezeti biztonsági és megfelelőségi kötelezettségvállalások teljesítése érdekében. Ha engedélyezve van a kettős titkosítás, a Storage-fiókban lévő adatai kétszer, a szolgáltatási szinten és egyszer az infrastruktúra szintjén vannak titkosítva, két különböző titkosítási algoritmus és két különböző kulcs használatával. |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_doubleEncryption.json) |
|[A virtuális hálózati befecskendezést engedélyezni kell az Azure Adatkezelő](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ad2fd1f-b25f-47a2-aa01-1a5a779e6413) |A hálózati peremhálózat biztonságossá tétele virtuális hálózati befecskendezéssel, amely lehetővé teszi a hálózati biztonsági csoportok szabályainak betartatását, a helyszíni csatlakoztatását és az adatkapcsolati források biztonságos elérését a szolgáltatási végpontokkal. |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_VNET_configured.json) |
