---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/08/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c8be26441afa3b13dc74a2f0e915bdd25cac633e
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2021
ms.locfileid: "98047298"
---
|Név<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Data Box feladatoknak engedélyezniük kell a kettős titkosítást az eszközön lévő inaktív adatok esetében](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc349d81b-9985-44ae-a8da-ff98d108ede8) |A szoftveres titkosítás második rétegének engedélyezése az eszközön tárolt adatokhoz. Az eszköz már védett a Advanced Encryption Standard 256 bites titkosításon keresztül a nyugalmi állapotban lévő adatokhoz. Ez a beállítás egy második adattitkosítási réteget is felvesz. |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_DoubleEncryption_Audit.json) |
|[Azure Data Box feladatoknak ügyfél által felügyelt kulccsal kell rendelkezniük az eszköz feloldási jelszavának titkosításához](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86efb160-8de7-451d-bc08-5d475b0aadae) |Az ügyfél által felügyelt kulcs segítségével szabályozhatja az eszköz zárolásának feloldásához szükséges jelszót a Azure Data Boxhoz. Az ügyfél által felügyelt kulcsok emellett segítenek a Data Box szolgáltatás által az eszköz zárolási jelszavának feloldásában, hogy előkészítse az eszközt és az Adatmásolást automatizált módon. Az eszközön lévő adatok már titkosítva vannak Advanced Encryption Standard 256 bites titkosítással, az eszköz zárolási jelszava pedig alapértelmezés szerint a Microsoft által felügyelt kulccsal van titkosítva. |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_CMK_Audit.json) |
