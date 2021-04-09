---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/24/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 299997dcfcd282b6e295cfef11f806fe74e53f11
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105033232"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Stack Edge-eszközöknek kettős titkosítást kell használniuk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4ac1030-89c5-4697-8e00-28b5ba6a8811) |Az eszközön lévő inaktív adatok védelméhez győződjön meg róla, hogy a rendszer duplán titkosítja, az adatokhoz való hozzáférést szabályozzák, és az eszköz inaktiválása után az adatok biztonságosan törlődnek az adatlemezekről. A kettős titkosítás két titkosítási réteget használ: a BitLocker XTS-AES 256 bites titkosítást az adatköteteken és a merevlemezek beépített titkosításával. További információt az adott stack Edge-eszköz biztonsági áttekintése dokumentációjában talál. |naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Stack%20Edge/AzureStackEdge_DoubleEncryption_Audit.json) |
