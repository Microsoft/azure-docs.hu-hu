---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7839d6be9eab20253c1518a550bcb2b6ebd3dcc4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866760"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Data Box feladatoknak engedélyezniük kell a dupla titkosítást az eszközön található, nem használt adatokhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc349d81b-9985-44ae-a8da-ff98d108ede8) |Engedélyezze a szoftveralapú titkosítás második rétegét az eszközön található, nem használt adatokhoz. Az eszköz már védve Advanced Encryption Standard 256 bites titkosítással az adatok számára. Ez a beállítás egy második adattitkosítási réteget ad hozzá. |Naplózás, Megtagadás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_DoubleEncryption_Audit.json) |
|[Azure Data Box feladatoknak ügyfél által felügyelt kulccsal kell titkosítaniuk az eszköz zárolásának feloldására használt jelszót](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86efb160-8de7-451d-bc08-5d475b0aadae) |Ügyfél által felügyelt kulccsal szabályozhatja az eszköz zárolásának feloldására vonatkozó jelszó titkosítását a Azure Data Box. Az ügyfél által kezelt kulcsok az eszköz zárolásának feloldására vonatkozó jelszó a Data Box-szolgáltatás által történő hozzáférésének kezelését is segítik az eszköz előkészítéséhez és az adatok automatikus másolásához. Az eszközön Advanced Encryption Standard található, 256 bites titkosítás már titkosítva van az eszközön, és az eszköz zárolásának feloldására használt jelszó alapértelmezés szerint egy Microsoft által felügyelt kulccsal van titkosítva. |Naplózás, Megtagadás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_CMK_Audit.json) |
