---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c68188ec1ee0c0e3a8d174bcb8c19d8a450c22dc
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498649"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Stack Edge eszközöknek kettős titkosítást kell használniuk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4ac1030-89c5-4697-8e00-28b5ba6a8811) |Az eszközön lévő inaktív adatok biztonságossá teéhez győződjön meg arról, hogy az adatok kettős titkosításúak, az adatokhoz való hozzáférés szabályozva van, és az eszköz inaktiválása után az adatok biztonságosan törlődnek az adatlemezről. A dupla titkosítás kétrétegű titkosítást használ: BitLocker XTS-AES 256 bites titkosítást az adatköteten, és a merevlemezek beépített titkosítását. További információt az adott Stack Edge-eszköz biztonsági áttekintési dokumentációjában talál. |audit, deny, disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Stack%20Edge/AzureStackEdge_DoubleEncryption_Audit.json) |
