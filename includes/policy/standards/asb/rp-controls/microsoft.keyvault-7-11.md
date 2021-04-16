---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8a6d26eb0fb795f0ca047ff4a976a5b31b9493ee
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107510965"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A kulcstartókban engedélyezni kell a végleges kiürítés elleni védelmet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |A kulcstartó rosszindulatú törlése végleges adatvesztéshez vezethet. A szervezet egy rosszindulatú belső felhasználója törölheti és kiürítheti a kulcstartókat. A végleges törlés elleni védelem megvédi a belső támadásokkal szemben azáltal, hogy kötelező megőrzési időszakot kényszerít ki a törölt kulcstartók számára. A szervezeten vagy a Microsofton belül senki sem fogja tudni véglegesen törölni a kulcstartókat a törlés megőrzési időszaka alatt. |Naplózás, Megtagadás, Letiltva |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |
