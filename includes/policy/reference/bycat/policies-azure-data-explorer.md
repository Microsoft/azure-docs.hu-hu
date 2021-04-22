---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: daac33f853d0f482c5a259e8135ed905d3154d08
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866726"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Data Explorer adattitkosításnak ügyfél által felügyelt kulcsot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81e74cea-30fd-40d5-802f-d72103c2aaaa) |Ha az adatfürtön ügyfél által felügyelt kulccsal engedélyezi az Azure Data Explorer titkosítást, az még nagyobb irányítást biztosít az adattitkosítás által használt kulcs felett. Ez a funkció gyakran a speciális megfelelőségi követelményekkel Key Vault ügyfelekre vonatkozik. |Naplózás, Megtagadás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_CMK.json) |
|[A lemeztitkosítást engedélyezni kell a Azure Data Explorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4b53539-8df9-40e4-86c6-6b607703bd4e) |A lemeztitkosítás engedélyezése segít megvédeni és védeni az adatokat, hogy megfeleljen a szervezeti biztonsági és megfelelőségi követelményeknek. |Naplózás, Megtagadás, Letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_disk_encrypted.json) |
|[A dupla titkosítást engedélyezni kell a Azure Data Explorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fec068d99-e9c7-401f-8cef-5bdde4e6ccf1) |A dupla titkosítás engedélyezése segít megvédeni és védeni az adatokat, hogy megfeleljen a szervezeti biztonsági és megfelelőségi követelményeknek. Ha a dupla titkosítás engedélyezve van, a tárfiókban található adatok titkosítása kétszer történik meg, egyszer a szolgáltatás szintjén, egyszer az infrastruktúra szintjén, két különböző titkosítási algoritmus és két különböző kulcs használatával. |Naplózás, Megtagadás, Letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_doubleEncryption.json) |
|[A virtuális hálózat injektálását engedélyezni kell a Azure Data Explorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ad2fd1f-b25f-47a2-aa01-1a5a779e6413) |Biztonságossá teszi a hálózat szegélyhálózatát virtuális hálózati injektálással, amely lehetővé teszi a hálózati biztonsági csoport szabályainak érvényesítését, a helyszíni csatlakozást és az adatkapcsolati források szolgáltatásvégpontokkal való biztonságát. |Naplózás, Megtagadás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_VNET_configured.json) |
