---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 341c6b0aca84afc88b0e611250ddd933cf9e6d48
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107881180"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A felügyelt SQL-példányok az ügyfelek által felügyelt kulcsokat használják az adatok titkosításához az](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |A transzparens adattitkosítás (TDE) saját kulccsal való megvalósítása nagyobb átláthatóságot és irányítást biztosít a TDE-védő felett, fokozott biztonságot nyújt egy HSM-háttérszolgáltatással, és lehetővé teszi a feladatok elkülönítését. Ez a javaslat a kapcsolódó megfelelőségi követelményekhez tartozó szervezetekre vonatkozik. |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[Az SQL-kiszolgálóknak ügyfél által felügyelt kulcsokat kell használniuk az adatok titkosításához](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |A transzparens adattitkosítás (TDE) saját kulccsal történő megvalósítása nagyobb átláthatóságot és irányítást biztosít a TDE-védő felett, nagyobb biztonságot nyújt a HSM-háttérszolgáltatással, és lehetővé teszi a feladatok elkülönítését. Ez a javaslat a kapcsolódó megfelelőségi követelményekhez tartozó szervezetekre vonatkozik. |AuditIfNotExists, Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[transzparens adattitkosítás SQL-adatbázisokon engedélyezni kell az adatbázis-adatokat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |A transzparens adattitkosítást engedélyezni kell az aktív adatok védelme és a megfelelőségi követelmények betartása érdekében |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |
