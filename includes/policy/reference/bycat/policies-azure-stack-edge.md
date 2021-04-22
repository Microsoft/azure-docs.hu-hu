---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 1913d07957adf71761e6fe65cb5bc9247f44219d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866711"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Stack Edge eszközöknek kettős titkosítást kell használniuk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4ac1030-89c5-4697-8e00-28b5ba6a8811) |Az eszközön lévő inaktív adatok adatvédelme érdekében győződjön meg arról, hogy duplán vannak titkosítva, az adatokhoz való hozzáférés szabályozva van, és az eszköz inaktiválása után az adatok biztonságosan törlődnek az adatlemezről. A dupla titkosítás két titkosítási réteget használ: BitLocker XTS-AES 256 bites titkosítást az adatköteten, és a merevlemezek beépített titkosítását. További információt az adott Stack Edge-eszköz biztonsági áttekintési dokumentációjában talál. |naplózás, tiltás, letiltás |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Stack%20Edge/AzureStackEdge_DoubleEncryption_Audit.json) |
